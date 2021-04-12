---
title: DTDL modellek
titleSuffix: Azure Digital Twins
description: Ismerje meg, hogy az Azure Digital Twins hogyan használja az egyéni modelleket a környezetben található entitások leírására.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: d3570a22fdd935237e673ea3e43ab5e463b66456
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104590534"
---
# <a name="understand-twin-models-in-azure-digital-twins"></a>Az Azure Digital Twinsbeli ikermodellek ismertetése

Az Azure Digital Twins egyik fő jellemzője, hogy saját szókincset definiálhat, és saját maga által definiált üzleti feltételeit felépítheti. Ezt a képességet a felhasználó által megadott **modelleken** keresztül biztosítjuk. A modelleket a világa leírásában szereplő nevekre lehet gondolni. 

A modell hasonlít egy objektumorientált programozási nyelv **osztályára** , amely egy adott koncepcióhoz tartozó adatalakzatot határoz meg a valós munkahelyi környezetben. A modellek névvel rendelkeznek (például a *Room* vagy a *hőmérséklet-érzékelő*), és olyan elemeket tartalmaznak, mint például a tulajdonságok, telemetria/események és parancsok, amelyek leírják, hogy milyen típusú entitást lehet a környezetben. Később ezeket a modelleket fogja használni, hogy olyan [**digitális ikreket**](concepts-twins-graph.md) hozzon létre, amelyek megfelelnek az ilyen típusú leírásnak megfelelő entitásoknak.

Az Azure digitális ikrek modelljei a JSON-LD-alapú **Digital Twin Definition Language (DTDL) nyelven** jelennek meg.  

## <a name="digital-twin-definition-language-dtdl-for-models"></a>Digital Twin Definition Language (DTDL) modellekhez

Az Azure Digital Twins modelljei a digitális Twins Definition Language (DTDL) használatával definiálhatók. 

Megtekintheti a GitHubon a DTDL teljes nyelvi specifikációit: [**Digital Twins Definition Language (DTDL) – 2-es verzió**](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).

A DTDL a JSON-LD nyelven alapul, és programnyelvtől független. A DTDL nem kizárólagos az Azure Digital Twins-ban, de más IoT-szolgáltatásokban, például a [IoT Plug and Playban](../iot-pnp/overview-iot-plug-and-play.md)is használhatók. Az Azure Digital Twins a DTDL **2. verzióját** használja (az Azure Digital Twins-val már elavult a DTDL 1. verziójának használata). 

A cikk további része összefoglalja, hogyan használják a nyelvet az Azure Digital Twinsban.

> [!NOTE] 
> Nem minden olyan szolgáltatás, amely a DTDL-t használja, pontosan ugyanazokat a funkciókat implementálja, mint a DTDL. Például a IoT Plug and Play nem használja a graphs szolgáltatáshoz használt DTDL-funkciókat, míg az Azure digitális Twins jelenleg nem implementál DTDL-parancsokat.
>
> Az Azure digitális Twins-ra jellemző DTDL-funkciókkal kapcsolatos további információkért tekintse meg a jelen cikk későbbi, az [Azure Digital Twins DTDL megvalósítási sajátosságai](#azure-digital-twins-dtdl-implementation-specifics)című szakaszát.

## <a name="elements-of-a-model"></a>A modell elemei

A modell definícióján belül a legfelső szintű kódrészlet egy **illesztőfelület**. Ez magában foglalja a teljes modellt, és a modell többi része az interfészen belül van definiálva. 

A DTDL-modell illesztőfelülete nulla, egy vagy több műveletet is tartalmazhat a következő mezők közül:
* **Tulajdonság** – a tulajdonságok olyan adatmezők, amelyek egy entitás állapotát jelölik (például a tulajdonságok számos objektumorientált programozási nyelven). A tulajdonságok biztonsági mentést végeznek, és bármikor olvashatók.
* Az **telemetria** -telemetria mezők mérési értékeket vagy eseményeket képviselnek, és gyakran használják az eszköz-érzékelők beolvasását. A tulajdonságoktól eltérően a telemetria nem digitális Twin-ben van tárolva; az időkorlátot tartalmazó adatesemények sorozata. A tulajdonság és a telemetria közötti különbségekről további információt a tulajdonságok és a [*telemetria*](#properties-vs-telemetry) című szakaszban talál.
* **Összetevő** – az összetevők lehetővé teszik, hogy a modell felületét más felületek szerelvényként hozza létre, ha szeretné. Egy összetevő például egy *frontCamera* felület (és egy másik összetevő-illesztőfelület *backCamera*), amely a modellnek a *telefonhoz* való definiálásához használatos. Először meg kell határoznia egy felületet a *frontCamera* , mintha a saját modellje lenne, majd a *telefon* definiálásakor hivatkozhat rá.

    Egy összetevővel leírhatja, hogy a megoldás szerves részét képezi-e, de nem igényel külön identitást, és nem kell egymástól függetlenül létrehozni, törölni vagy átrendezni a különálló gráfban. Ha azt szeretné, hogy az entitások a Twin gráfban független létezéssel rendelkezzenek, akkor a *kapcsolatok* (lásd a következő felsorolást) külön digitális twinsként képviseljék őket.
    
    >[!TIP] 
    >Az összetevők a szervezethez is felhasználhatók, és a kapcsolódó tulajdonságok csoportjai a modell felületén belül csoportosíthatók. Ebben az esetben az egyes összetevőket névtérként vagy "mappaként" tekintheti meg az illesztőfelületen belül.
* **Kapcsolat** – a kapcsolatok lehetővé teszik, hogy egy digitális Twin-et más digitális ikrek is felvegyen. A kapcsolatok különböző szemantikai jelentéseket jelenthetnek, például a következőt: *tartalmaz* ("a padló tartalmaz szobát"), *a (z) ("* HVAC Coolers Room"), a *isBilledTo* ("kompresszor számlázása felhasználó") stb. A kapcsolatok lehetővé teszik a megoldás számára az egymással összefüggő entitások gráfjának megadását.

> [!NOTE]
> A [DTDL specifikációja](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) olyan **parancsokat** is definiál, amelyek olyan metódusok, amelyek a digitális twin (például egy alaphelyzetbe állítási parancs, vagy a ventilátor be-és kikapcsolására szolgáló parancs) esetében hajthatók végre. *A parancsok azonban jelenleg nem támogatottak az Azure Digital Twins-ben.*

### <a name="properties-vs-telemetry"></a>Tulajdonságok és telemetria

Íme néhány további útmutatás a DTDL **tulajdonság** és a **telemetria** mezők megkülönböztetéséhez az Azure digitális Twins-ban.

Az Azure digitális ikrek modelljeinek tulajdonságai és telemetria közötti különbség a következő:
* A **tulajdonságokat** a rendszer a tárolók biztonsági mentésére vár. Ez azt jelenti, hogy bármikor beolvashat egy tulajdonságot, és lekérheti annak értékét. Ha a tulajdonság írható, egy értéket is tárolhat a tulajdonságban.  
* A **telemetria** több, mint az események streamje; Ez a rövid élettartamú adatüzenetek halmaza. Ha nem állítja be az esemény figyelését, és ha ez történik, akkor az eseményt nem lehet egy későbbi időpontban nyomon követni. Nem térhet vissza hozzá, és később is elolvashatja. 
  - A C# kifejezésekben a telemetria olyan, mint egy C# esemény. 
  - A IoT feltételeiben a telemetria általában egyetlen, az eszköz által eljuttatott mérték.

A **telemetria** gyakran használják a IoT-eszközökhöz, mert számos eszköz nem képes vagy érdekli a általa generált mérési értékek tárolása. Csak a "telemetria" események streamjét küldik el rájuk. Ebben az esetben a telemetria mező legújabb értékének megkérdezése nem lehetséges az eszközön. Ehelyett meg kell hallgatni az eszközön lévő üzeneteket, és el kell végeznie a műveleteket az üzenetek érkezésekor. 

Ennek eredményeképpen, amikor modelleket tervez az Azure Digital Twinsban, valószínűleg a legtöbb esetben a **tulajdonságokat** fogja használni az ikrek modellezéséhez. Ez lehetővé teszi a biztonsági mentést, valamint az adatmezők olvasását és lekérdezését.

A telemetria és a tulajdonságok gyakran együtt működnek az eszközökről érkező adatforgalom kezelésére. Mivel az Azure digitális Twins-ba való belépés az [API](how-to-use-apis-sdks.md)-kon keresztül történik, általában a beáramlási függvényt használja az eszközökről származó telemetria-vagy tulajdonság-események olvasására, és válaszként egy tulajdonságot állít be az Azure Digital Twins-ban. 

Telemetria eseményt is közzétehet az Azure digitális Twins API-ból. Akárcsak más telemetria, ez egy rövid életű esemény, amelyhez figyelőt kell kezelni.

### <a name="azure-digital-twins-dtdl-implementation-specifics"></a>Azure digitális Twins DTDL implementációs sajátosságai

Ahhoz, hogy egy DTDL-modell kompatibilis legyen az Azure Digital Twins szolgáltatással, meg kell felelnie ezeknek a követelményeknek.

* A modell minden legfelső szintű DTDL *illesztőfelület* típusúnak kell lennie. Ennek az az oka, hogy az Azure digitális Twins-modell API-jai olyan JSON-objektumokat fogadhatnak, amelyek a felületet vagy az illesztőfelületek tömbjét jelölik. Ennek eredményeképpen a legfelső szinten nem engedélyezettek más DTDL-típusok.
* Az Azure Digital Twins DTDL nem lehet *parancsokat* definiálni.
* Az Azure Digital Twins csak egyetlen szinten teszi lehetővé az összetevők beágyazását. Ez azt jelenti, hogy egy összetevőként használt felületnek nem lehet saját összetevője. 
* Az illesztőfelületek nem definiálhatók más DTDL-felületeken belüli beágyazott kapcsolaton belül. azokat különálló legfelső szintű entitásként kell definiálni, amelyeknek saját azonosítójuk van. Ezt követően, ha egy másik csatoló összetevőként vagy öröklés útján szeretné felvenni az illesztőfelületet, hivatkozhat az AZONOSÍTÓra.

Az Azure Digital Twins nem veszi figyelembe az `writable` attribútumot a tulajdonságok és a kapcsolatok esetében is. Bár ez DTDL-specifikációként is beállítható, az Azure Digital Twins nem használja az értéket. Ehelyett ezeket a rendszer az Azure Digital Twins szolgáltatáshoz általános írási engedéllyel rendelkező külső ügyfelek által írhatóként kezeli.

## <a name="example-model-code"></a>Példa a modell kódjára

A Twin Type modellek bármilyen szövegszerkesztőben megírhatók. A DTDL nyelv JSON-szintaxist követ, ezért a *. JSON* kiterjesztésű modelleket kell tárolnia. A JSON-bővítmény használatával számos programozási szövegszerkesztő használható a DTDL-dokumentumok alapvető szintaxisának ellenőrzéséhez és kiemeléséhez. A [Visual Studio Code](https://code.visualstudio.com/)-hoz a [DTDL bővítmény](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) is elérhető.

Ez a szakasz egy tipikus modellt tartalmaz, amely DTDL felületként íródott. A modell leírja a **bolygókat**, amelyek mindegyike névvel, tömeggel és hőmérséklettel rendelkezik.
 
Vegye figyelembe, hogy a **bolygók a** műholdakat is használhatják, és **krátereket** is tartalmazhatnak. Az alábbi példában a `Planet` modell két külső modellel (és) hivatkozik a többi entitáshoz való kapcsolódásra `Moon` `Crater` . Ezeket a modelleket az alábbi példában is meg kell határozni, de nagyon egyszerűek maradnak, hogy ne lehessen az elsődleges `Planet` példából kivonni.

:::code language="json" source="~/digital-twins-docs-samples/models/Planet-Crater-Moon.json":::

A modell mezői a következők:

| Mező | Leírás |
| --- | --- |
| `@id` | A modell azonosítója. Formátumúnak kell lennie `dtmi:<domain>:<unique model identifier>;<model version number>` . |
| `@type` | A leírt információ típusát azonosítja. Illesztőfelület esetén a típus *illesztőfelület*. |
| `@context` | Beállítja a JSON-dokumentum [kontextusát](https://niem.github.io/json/reference/json-ld/context/) . A modelleknek használatban kell lenniük `dtmi:dtdl:context;2` . |
| `displayName` | választható Lehetővé teszi, hogy a modell felhasználóbarát nevet adjon, ha szükséges. |
| `contents` | Az összes többi illesztőfelület-adattal ide kerül, mint az attribútumok definícióinak tömbje. Minden attribútumnak `@type` (*tulajdonság*, *telemetria*, *parancs*, *kapcsolat* vagy *összetevő*) meg kell adnia az általa leírt illesztőfelület-információk, majd a tényleges attribútumot definiáló tulajdonságok készletét (például egy `name` `schema` *tulajdonság* definiálását). |

> [!NOTE]
> Vegye figyelembe, hogy az összetevő felülete (ebben a példában a *kráter* ) ugyanabban a tömbben van definiálva, mint az azt használó felület (*Planet*). Az összetevőket úgy kell meghatározni, hogy az API-hívások megtalálhatók legyenek az illesztőfelületen.

### <a name="possible-schemas"></a>Lehetséges sémák

A DTDL-hez hasonlóan a *tulajdonság* -és *telemetria* -attribútumok sémája szabványos primitív típusok –,, `integer` `double` `string` és `Boolean` – és más típusok (például `DateTime` és) lehetnek `Duration` . 

Az egyszerű típusok mellett a *Property* és a *telemetria* mezők a következő összetett típusokkal rendelkezhetnek:
* `Object`
* `Map`
* `Enum`

A *telemetria* mezők is támogatják a használatát `Array` .

### <a name="model-inheritance"></a>Modell öröklése

Időnként előfordulhat, hogy további modelleket szeretne specializálni. Előfordulhat *például, hogy egy általános modellel rendelkezik*, és speciális változatok *ConferenceRoom* és *edzőteremmel* rendelkezik. A specializáció kifejezéséhez a DTDL támogatja az öröklést: az illesztőfelületek örökölni tudnak egy vagy több másik felületről. 

Az alábbi példa a korábbi DTDL származó *bolygó* modelljét képzeli el újra egy nagyobb *CelestialBody* -modell altípusa szerint. A "Parent" modell először van definiálva, majd a "gyermek" modell a mező használatával épít rá `extends` .

:::code language="json" source="~/digital-twins-docs-samples/models/CelestialBody-Planet-Crater.json":::

Ebben a példában a *CelestialBody* a *bolygó* nevét, tömegét és hőmérsékletét is segíti. A `extends` szakasz az illesztőfelület neve vagy az illesztőfelületek neveinek tömbje (ha szükséges, a több szülő modellből örökölheti a felületet).

Az öröklés alkalmazása után a kibővítési felület az összes tulajdonságot megjeleníti a teljes öröklési láncból.

A kiterjesztési felület nem változtathatja meg a szülő felületek definícióit; csak azok hozzáadására használható. Emellett nem tud újradefiniálni egy már definiált képességet a szülő felületén (még akkor is, ha a képességek definiálva vannak). Ha például egy szülő felület egy `double` tulajdonság *tömegét* határozza meg, akkor a kiterjesztési felület nem tartalmazhat *tömeges* deklarációt, még akkor is, ha az is a `double` .

## <a name="best-practices-for-designing-models"></a>Ajánlott eljárások modellek tervezéséhez

A modellek a környezetében lévő entitások megjelenítésére való megtervezése során hasznos lehet előre megtekinteni [a tervezési szempontokat](concepts-query-language.md) . Előfordulhat, hogy a tulajdonságokat úgy kell megtervezni, hogy elkerülje a diagram bejárásának nagy eredményeinek elkerülő módját. Érdemes lehet olyan kapcsolatokat is modellezni, amelyek egyetlen lekérdezésben válaszolnak egyszintű kapcsolatként.

### <a name="validating-models"></a>Modellek ellenőrzése

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="tools-for-models"></a>Modellek eszközei 

Számos példa áll rendelkezésre, hogy még könnyebb legyen a modellek és a ontológiákat kezelése. Ebben az adattárban találhatók: [eszközök a digitális Twins Definition Language (DTDL) eszközhöz](https://github.com/Azure/opendigitaltwins-tools).

Ez a szakasz részletesen ismerteti a minták aktuális készletét.

### <a name="model-uploader"></a>Modell feltöltője 

_**Modellek feltöltése az Azure Digital Twinsba**_

Ha elkészült a modellek létrehozásával, kiterjesztésével vagy kiválasztásával, feltöltheti őket az Azure Digital Twins-példányba, hogy azok elérhetők legyenek a megoldásban. Ezt az [Azure Digital Twins API](how-to-use-apis-sdks.md)-k használatával végezheti el, a [*útmutató: DTDL-modellek kezelése*](how-to-manage-model.md#upload-models)című témakörben leírtak szerint.

Ha azonban sok modell van feltöltve – vagy ha sok egymásrautaltsága lenne az egyéni feltöltések megrendeléséhez –, a minta használatával egyszerre több modellt is feltölthet: az [**Azure digitális Twins-modell feltöltője**](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/ModelUploader). Kövesse a minta konfigurálásához és használatához szükséges utasításokat a modelleknek a saját példányba való feltöltéséhez.

### <a name="model-visualizer"></a>Modell megjelenítő 

_**Modellek megjelenítéséhez**_

Miután feltöltötte a modelleket az Azure Digital Twins-példányba, megtekintheti az Azure digitális Twins-példányának modelljeit, beleértve az öröklési és a modellbeli kapcsolatokat is az [**Azure digitális Twins-modell megjelenítő**](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/AdtModelVisualizer)funkciójának használatával. Ez a minta jelenleg vázlat állapotban van. Javasoljuk, hogy a digitális Twins fejlesztői közösség kiterjessze és hozzájáruljanak a mintához. 

## <a name="next-steps"></a>Következő lépések

* Ismerje meg az iparági szabványnak megfelelő ontológiákat alapuló modellek létrehozását: [ *fogalmak: Mi az az ontológia?*](concepts-ontologies.md)

* A modellek kezelése az API-műveletekkel: [ *útmutató: DTDL-modellek kezelése*](how-to-manage-model.md)

* Ismerje meg, hogyan használhatók a modellek a digitális Twins létrehozásához: [ *fogalmak: digitális ikrek és a Twin Graph*](concepts-twins-graph.md)


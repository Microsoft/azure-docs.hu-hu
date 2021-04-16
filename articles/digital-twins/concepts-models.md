---
title: DTDL-modellek
titleSuffix: Azure Digital Twins
description: Megtudhatja, Azure Digital Twins egyéni modelleket használ a környezetben az entitások leírására.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 8942262c2e02670d57b1db324eb154dcc38f00f8
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575394"
---
# <a name="understand-twin-models-in-azure-digital-twins"></a>Az Azure Digital Twinsbeli ikermodellek ismertetése

Az üzleti Azure Digital Twins egyik fő jellemzője, hogy meghatározhatja saját szókincsét, és az ikergráfot az üzlet ön által meghatározott fogalmai szerint építi fel. Ezt a képességet a felhasználó által biztosított modellek **biztosítják.** A modellek a világ leírásában főnevekként is feletetnek. 

A modellek hasonlóak **az** objektumorientált programozási nyelvek osztályához, és adatalakokat definiálnak egy adott fogalomhoz a valós munkahelyi környezetben. A modellek nevei  (például Room vagy *TemperatureSensor),* és olyan elemeket tartalmaznak, mint a tulajdonságok, a telemetria/események és a parancsok, amelyek leírják, hogy mire képes ez a típusú entitás a környezetben. Később ezekkel a modellekkel [](concepts-twins-graph.md) fog digitális ikereket létrehozni, amelyek az adott típus leírásának megfelelő adott entitásokat képviselik.

Azure Digital Twins modelleket a JSON-LD-alapú **Digital Twin Definition Language (DTDL) képviseli.**  

## <a name="digital-twin-definition-language-dtdl-for-models"></a>Digital Twin Definition Language (DTDL) modellekhez

A Azure Digital Twins modellek az Digital Twins Definition Language (DTDL) használatával vannak definiálva. 

A DTDL összes nyelvi specifikációját megtekintheti a GitHubon: Digital Twins [**Definition Language (DTDL) – 2-es verzió.**](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)

A DTDL a JSON-LD nyelven alapul, és programnyelvtől független. A DTDL nem csak Azure Digital Twins, hanem más IoT-szolgáltatásokban, például az [IoT-Plug and Play.](../iot-pnp/overview-iot-plug-and-play.md) Azure Digital Twins A DTDL **2-es** verzióját használja (a DTDL 1- Azure Digital Twins használata elavult). 

A cikk hátralévő részében összefoglaljuk, hogyan használható a Azure Digital Twins.

### <a name="azure-digital-twins-dtdl-implementation-specifics"></a>Azure Digital Twins DTDL implementációval kapcsolatos részletek

Nem minden DTDL-t használt szolgáltatás valósítja meg pontosan a DTDL ugyanazon funkcióit. Az IoT-Plug and Play például nem használja a gráfok DTDL-funkcióit, Azure Digital Twins jelenleg nem implementálja a DTDL-parancsokat. 

Ahhoz, hogy egy DTDL-modell kompatibilis legyen a Azure Digital Twins, meg kell felelnie a következő követelményeknek:

* A modell összes legfelső szintű DTDL-elemének típusúnak kell *lennie.* Ennek az az oka Azure Digital Twins hogy a modell API-k JSON-objektumokat fogadhatnak, amelyek egy felületet vagy egy felülettömböt képviselnek. Ennek eredményeképpen más DTDL-elemtípusok nem engedélyezettek a legfelső szinten.
* A DTDL Azure Digital Twins nem definiálhat *parancsokat.*
* Azure Digital Twins az összetevők beágyazása csak egyetlen szintet tesz lehetővé. Ez azt jelenti, hogy az összetevőként használt interfészek nem tartalmaznak összetevőket. 
* A felületek nem definiálhatóak beágyazottan más DTDL-felületeken belül; ezeket külön felső szintű entitásokként kell definiálni, saját azonosítójukkal. Ha egy másik felület ezt a felületet összetevőként vagy öröklés útján szeretné tartalmazni, hivatkozhat az azonosítójára.

Azure Digital Twins a tulajdonságok vagy kapcsolatok `writable` attribútumát sem figyeli meg. Bár ez a DTDL-specifikációk szerint is beállítható, a DTDL-specifikációk nem használják ezt az Azure Digital Twins. Ehelyett ezeket a külső ügyfelek mindig írhatóként kezelik, amelyek általános írási engedélyekkel rendelkezik a Azure Digital Twins számára.

## <a name="elements-of-a-model"></a>A modell elemei

A modelldefiníción belül a legfelső szintű kódelem egy **interfész.** Ez magában foglalja a teljes modellt, és a modell többi része az interfészen belül van definiálva. 

A DTDL-modell felülete nulla, egy vagy több mezőt tartalmazhat az alábbi mezők közül:
* **Tulajdonság** – A tulajdonságok olyan adatmezők, amelyek egy entitás állapotát jelölik (sok objektumorientált programozási nyelv tulajdonságaihoz hasonlók). A tulajdonságok rendelkeznek háttértárhellyel, és bármikor olvashatók.
* **Telemetria** – A telemetriamezők méréseket vagy eseményeket képviselnek, és gyakran használatosak az eszközérzékelők mérései leírására. A tulajdonságokkal ellentétben a telemetria nem digitális ikereszközben van tárolva; időkorrekta adatesemények sorozata, amelyek a bekövetkeztükkor kezelhetők. A tulajdonság és a telemetria közötti különbségekkel kapcsolatos további információkért tekintse meg az alábbi [*Tulajdonságok és telemetria*](#properties-vs-telemetry) szakaszt.
* **Összetevő** – Az összetevők lehetővé teszik, hogy a modellfelületet más felületek szerelvényeként építse fel, ha szeretné. Ilyen összetevő például a *frontCamera* felület (és egy másik *backCamera* összetevő-interfész), amely egy telefon modelljének *definiálása során használatos.* Először úgy kell definiálni a *frontCamera* felületét, mintha saját modell lenne, majd hivatkozhat rá a *Phone meghatározásakor.*

    Egy összetevővel leírható valami, ami a megoldás szerves része, de nincs szükség külön identitásra, és nem kell külön létrehozni, törölni vagy átrendezni az ikergráfban. Ha azt szeretné, hogy az entitások egymástól független létezik-e az ikergráfban, azokat különböző modellek különálló, kapcsolatok által összekapcsolt digitális ikereszközeiként ábrázolja *(lásd* a következő ábrát).
    
    >[!TIP] 
    >Az összetevők a szervezéshez is használhatók, így a modellfelületen belül csoportosíthatóak a kapcsolódó tulajdonságok. Ebben az esetben az egyes összetevők a felületen belüli névtérként vagy "mappáként" gondolnak.
* **Kapcsolat** – A kapcsolatokkal ábrázolhatja, hogyan lehet egy digitális iker egy másik digitális ikereszközben részt venni. A kapcsolatok különböző szemantikai jelentésekkel is bírnak, például tartalmaznak *("padló* tartalmaz helyiséget"), *cools* ("hvac cools room"), *isBilledTo* ("a szoba a felhasználónak van számlázva") stb. A kapcsolatok lehetővé teszik, hogy a megoldás gráfot adjon a relációban található entitásokhoz.

> [!NOTE]
> A [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) specifikációja a parancsokat is **meghatározza,** amelyek a digitális ikeren végrehajtható metódusok (például egy alaphelyzetbe állítási parancs vagy egy ventilátor be- vagy kikapcsolása). A *parancsok azonban jelenleg nem támogatottak a Azure Digital Twins.*

### <a name="properties-vs-telemetry"></a>Tulajdonságok és telemetria

Az alábbi útmutató további útmutatást nyújt a  DTDL-tulajdonság- és **telemetriamezők** megkülönböztetésére a Azure Digital Twins.

A tulajdonságok és a telemetria közötti különbség a Azure Digital Twins modellek esetében a következő:
* **A tulajdonságoknak** háttértárolóval kell rendelkeznek. Ez azt jelenti, hogy egy tulajdonságot bármikor beolvashat, és lekérheti annak értékét. Ha a tulajdonság írható, akkor a tulajdonságban is tárolhat értéket.  
* **A telemetria** inkább egy eseménystreamhez hasonló; Ez egy rövid élettartamú adatüzenetek halmaza. Ha nem kell figyelnie az eseményt és az esemény bekövetkeztének műveletét, a későbbiekben nem lesz nyomkövetés az eseményről. Később már nem lehet visszajönni hozzá, és elolvasni. 
  - C# kifejezés szerint a telemetria olyan, mint egy C#-esemény. 
  - IoT-kifejezéssel tekintve a telemetria általában egy eszköz által küldött mérés.

**A telemetriát** gyakran használják IoT-eszközökkel, mert sok eszköz nem képes vagy érdekli a generált mérési értékek tárolása. Egyszerűen "telemetriai" események streameként küldik el őket. Ebben az esetben nem kérdezheti le bármikor az eszközön a telemetriamező legfrissebb értékét. Ehelyett figyelnie kell az eszközről érkező üzeneteket, és műveleteket kell végre vennie az üzenetek érkezésekor. 

Ennek eredményeképpen, amikor egy modellt a Azure Digital Twins tervez,  valószínűleg a legtöbb esetben tulajdonságokat fog használni az ikermodellezéshez. Ez lehetővé teszi a háttértárat, valamint az adatmezők olvasását és lekérdezését.

A telemetria és a tulajdonságok gyakran együtt kezelik az eszközökről bejövő adatokat. Mivel a bejövő forgalom Azure Digital Twins [](how-to-use-apis-sdks.md)API-kon keresztül van beállítva, általában a bejövő forgalom függvényét fogja használni az eszközök telemetria- vagy tulajdonságeseményeit beolvassa, és válaszként beállít egy tulajdonságot a Azure Digital Twins- és tulajdonságként. 

A telemetriai eseményeket a Azure Digital Twins API-ból is közzéteheti. Mint más telemetriához, ez is egy rövid életű esemény, amelyhez a figyelőnek kell kezelnie.

## <a name="model-inheritance"></a>Modellöröklés

Előfordulhat, hogy tovább szeretne specializálni egy modellt. Hasznos lehet például, ha van egy általános modellterme, és a ConferenceRoom és a *ConferenceRoom* speciális *változatai.* A specializáció kifejezéséhez a DTDL támogatja az öröklést: a felületek egy vagy több más felülettől örökölhetnek. 

Az alábbi példa a korábbi DTDL-példából származó *Planet* modellt egy nagyobb *CelestialBody* modell altípusának képzeli el. Először a "szülő" modell van definiálva, majd a "gyermek" modell épül rá a mező `extends` használatával.

:::code language="json" source="~/digital-twins-docs-samples/models/CelestialBody-Planet-Crater.json":::

Ebben a példában a *CelestialBody* egy nevet, egy tömeget és egy hőmérsékletet ad a *Planetnek.* A szakasz egy felületnév vagy interfésznevek tömbje (amely lehetővé teszi, hogy a kibővíthető felület szükség esetén több szülőmodelltől is `extends` örökölhető).

Az öröklés alkalmazása után a kibővítő felület a teljes öröklési lánc összes tulajdonságát elérhetővé teszi.

A kibővíthető felület nem módosíthatja a szülőfelületek definícióit; csak hozzáadhatja őket. A szülőillesztőkben már definiált képességeket sem tudja újradefiniálni (még akkor sem, ha a képességek ugyanazokként vannak definiálva). Ha például a szülőfelület egy tulajdonság tömegét definiálja, a kibővíthető felület nem tartalmazhat tömeges deklarációt, még akkor sem, ha `double` egyben   `double` .

## <a name="model-code"></a>Modellkód

Az ikermodellek bármilyen szövegszerkesztőben megírhatóak. A DTDL nyelv a JSON-szintaxist követi, ezért *a .json* kiterjesztésű modelleket érdemes tárolni. A JSON-bővítmény használatával számos programozási szövegszerkesztő biztosít alapszintű szintaxis-ellenőrzést és kiemelést a DTDL-dokumentumokhoz. A Code-hez [egy DTDL-Visual Studio](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) [is elérhető.](https://code.visualstudio.com/)

### <a name="possible-schemas"></a>Lehetséges sémák

A DTDL-nek  megfelelően a Tulajdonság és *Telemetria* attribútumok sémája standard primitív típusokból ( , , és ) és más típusokból is lehet, például és `integer` `double` `string` `Boolean` `DateTime` `Duration` . 

A primitív típusok mellett a *Tulajdonság és* *a Telemetria* mező is a következő összetett típusokkal is lehet:
* `Object`
* `Map`
* `Enum`

*A telemetriamezők* a -t is `Array` támogatják.

### <a name="example-model"></a>Példamodell

Ez a szakasz egy tipikus modellre mutat példát, amely DTDL-felületként van megírva. A modell a **nevű,** a tömegű és a hőmérsékletű bolygókat írja le.
 
Vegye figyelembe, hogy a  bolygók holdokkal is interakcióba léphetnek, amelyek a holdjaik, és **cratereket tartalmazhatnak.** Az alábbi példában a modell két külső modellre , a és a típusra hivatkozva fejezi ki a kapcsolatokat ezekkel a más `Planet` `Moon` `Crater` entitásokkal. Ezek a modellek az alábbi példakódban is definiálva vannak, de nagyon egyszerűek, hogy ne távolodnak el az elsődleges `Planet` példától.

:::code language="json" source="~/digital-twins-docs-samples/models/Planet-Crater-Moon.json":::

A modell mezői a következőek:

| Mező | Leírás |
| --- | --- |
| `@id` | A modell azonosítója. A következő formátumban kell `dtmi:<domain>:<unique model identifier>;<model version number>` lennie: . |
| `@type` | A leírt információ fajtáját azonosítja. Egy felülethez a típus *Interface*. |
| `@context` | Beállítja [a](https://niem.github.io/json/reference/json-ld/context/) JSON-dokumentum környezetét. A modelleknek a típust kell `dtmi:dtdl:context;2` használniuk. |
| `displayName` | [nem kötelező] Lehetővé teszi, hogy szükség esetén rövid nevet adjon a modellnek. |
| `contents` | Itt az összes többi interfészadatot attribútumdefiníciók tömbjeként helyezzük el. Minden attribútumnak meg kell adnia egy `@type` ( tulajdonságot, *telemetriát,* parancsot,  kapcsolatot vagy összetevőt) az adott interfészinformáció azonosításához, majd a tényleges attribútumot meghatározó tulajdonságokat (például és tulajdonság definiálását). `name` `schema`  |

> [!NOTE]
> Vegye figyelembe, hogy az összetevő felület ( ebben a példában a *Crater)* ugyanabban a tömbben van definiálva, mint az azt használó felület (*Planet*). Az összetevőket így kell definiálni az API-hívásokban, hogy a felület megtalálható legyen.

## <a name="best-practices-for-designing-models"></a>Ajánlott eljárások a modellek tervezéshez

A környezetben az entitásokat tükröző modellek tervezése során hasznos lehet [](concepts-query-language.md) előretekolni, és megfontolni a tervezés lekérdezési következményeit. A tulajdonságokat úgy kell tervezni, hogy elkerülhetők a gráfbejárásból származó nagy eredményhalmazok. Emellett olyan kapcsolatokat is modellehet, amelyekre egyetlen lekérdezésben egyszintű kapcsolatként kell választ adni.

### <a name="validating-models"></a>Modellek validálása

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="tools-for-models"></a>Modellekhez használható eszközök 

Számos minta érhető el, amelyek még egyszerűbbé teszik a modellek és a kilogikák egyszerűbben való kezelhetőségét. Ezek a következő adattárban találhatók: [Tools for Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-tools).

Ez a szakasz részletesebben ismerteti a minták aktuális készletét.

### <a name="model-uploader"></a>Modellfeltöltő 

_**Modellek feltöltése Azure Digital Twins**_

Miután befejezte a modellek létrehozását, bővítését vagy kiválasztását, feltöltheti őket a Azure Digital Twins-példányra, hogy elérhetővé tegye őket a megoldásban való használatra. Ez a következő Azure Digital Twins [érhető](how-to-use-apis-sdks.md) [*el: How-to: Manage DTDL models (DTDL-modellek kezelése).*](how-to-manage-model.md#upload-models)

Ha azonban sok modellt kell feltöltenie – vagy ha sok függősége van, amelyek bonyolulttá tennék az egyes feltöltések megrendelését – ezt a mintát használhatja egyszerre több modell feltöltésére: [**Azure Digital Twins Model Uploader**](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/ModelUploader). A minta utasításait követve konfigurálhatja és használhatja ezt a projektet a modellek saját példányba való feltöltéséhez.

### <a name="model-visualizer"></a>Modell-vizualizáló 

_**Modellek vizualizációja**_

Miután feltöltötte a modelleket a Azure Digital Twins-példányba, megtekintheti a modelleket a Azure Digital Twins-példányban, beleértve az öröklési és modellkapcsolatokat is, a [**Azure Digital Twins Model Visualizer használatával.**](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/AdtModelVisualizer) Ez a minta jelenleg piszkozat állapotban van. Arra bátorítjuk a Digital Twins fejlesztői közösségét, hogy bővítsen ki és járuljon hozzá a mintához. 

## <a name="next-steps"></a>Következő lépések

* Megtudhatja, hogyan lehet modelleket létrehozni az iparági szabványnak megfelelő ontológiák alapján: [ *Fogalmak: Mi az az ontológia?*](concepts-ontologies.md)

* A modellek API-műveletekkel való kezelésének mélyebb mélyebbreása: [ *How-to: Manage DTDL models (A DTDL-modellek kezelése)*](how-to-manage-model.md)

* Megtudhatja, hogyan használhatók a modellek a digitális ikerképek létrehozásához: [ *Fogalmak: Digitális ikerképek és az ikergráf*](concepts-twins-graph.md)


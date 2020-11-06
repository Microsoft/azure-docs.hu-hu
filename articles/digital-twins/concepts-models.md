---
title: Egyéni modellek
titleSuffix: Azure Digital Twins
description: Ismerje meg, hogy az Azure Digital Twins hogyan használja a felhasználó által definiált modelleket a környezetben található entitások leírására.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 12eed6aeccffe854810e9c2ddc8a5c4e59b8c312
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2020
ms.locfileid: "94337933"
---
# <a name="understand-twin-models-in-azure-digital-twins"></a>A Twin modellek ismertetése az Azure Digital Twinsban

Az Azure Digital Twins egyik fő jellemzője, hogy saját szókincset definiálhat, és saját maga által definiált üzleti feltételeit felépítheti. Ezt a képességet felhasználó által definiált **modelleken** keresztül biztosítjuk. A modelleket a világa leírásában szereplő nevekre lehet gondolni. 

A modell hasonlít egy objektumorientált programozási nyelv **osztályára** , amely egy adott koncepcióhoz tartozó adatalakzatot határoz meg a valós munkahelyi környezetben. A modellek névvel rendelkeznek (például a *Room* vagy a *hőmérséklet-érzékelő* ), és olyan elemeket tartalmaznak, mint például a tulajdonságok, telemetria/események és parancsok, amelyek leírják, hogy milyen típusú entitást lehet a környezetben. Később ezeket a modelleket fogja használni, hogy olyan [**digitális ikreket**](concepts-twins-graph.md) hozzon létre, amelyek megfelelnek az ilyen típusú leírásnak megfelelő entitásoknak.

Az Azure digitális ikrek modelljei a JSON-LD-alapú **Digital Twin Definition Language (DTDL) nyelven** jelennek meg.  

## <a name="digital-twin-definition-language-dtdl-for-models"></a>Digital Twin Definition Language (DTDL) modellekhez

Az Azure Digital Twins modelljei a digitális Twins Definition Language (DTDL) használatával definiálhatók. A DTDL JSON-LD-alapú, és a programozási nyelvtől független. A DTDL nem kizárólagos az Azure Digital Twins-ban, de más IoT-szolgáltatásokban, például a [IoT Plug and Playban](../iot-pnp/overview-iot-plug-and-play.md)is használhatók. 

Az Azure Digital Twins a **DTDL _2-es verzióját_** használja. A DTDL ezen verziójával kapcsolatos további információkért tekintse meg a specifikációs dokumentációt a GitHubon: [*Digital Twins Definition Language (DTDL) – 2. verzió*](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md). Az DTDL _1-es verziójának_ használata az Azure Digital Twins használatával már elavult.

> [!NOTE] 
> Nem minden olyan szolgáltatás, amely a DTDL-t használja, pontosan ugyanazokat a funkciókat implementálja, mint a DTDL. Például a IoT Plug and Play nem használja a graphs szolgáltatáshoz használt DTDL-funkciókat, míg az Azure digitális Twins jelenleg nem implementál DTDL-parancsokat.
>
> Az Azure digitális Twins-ra jellemző DTDL-funkciókkal kapcsolatos további információkért tekintse meg a jelen cikk későbbi, az [Azure Digital Twins DTDL megvalósítási sajátosságai](#azure-digital-twins-dtdl-implementation-specifics)című szakaszát.

## <a name="elements-of-a-model"></a>A modell elemei

A modell definícióján belül a legfelső szintű kódrészlet egy **illesztőfelület**. Ez magában foglalja a teljes modellt, a modell többi része pedig az illesztőfelületen belül van definiálva. 

A DTDL-modell illesztőfelülete nulla, egy vagy több műveletet is tartalmazhat a következő mezők közül:
* **Tulajdonság** – a tulajdonságok olyan adatmezők, amelyek egy entitás állapotát jelölik (például a tulajdonságok számos objektumorientált programozási nyelven). A tulajdonságok biztonsági mentést végeznek, és bármikor olvashatók.
* Az **telemetria** -telemetria mezők mérési értékeket vagy eseményeket képviselnek, és gyakran használják az eszköz-érzékelők beolvasását. A tulajdonságoktól eltérően a telemetria nem digitális Twin-ben van tárolva; az időkorlátot tartalmazó adatesemények sorozata. A tulajdonság és a telemetria közötti különbségekről további információt a tulajdonságok és a [*telemetria*](#properties-vs-telemetry) című szakaszban talál.
* **Összetevő** – az összetevők lehetővé teszik, hogy a modell felületét más felületek szerelvényként hozza létre, ha szeretné. Egy összetevő például egy *frontCamera* felület (és egy másik összetevő-illesztőfelület *backCamera* ), amely a modellnek a *telefonhoz* való definiálásához használatos. Először meg kell határoznia egy felületet a *frontCamera* , mintha a saját modellje lenne, majd a *telefon* definiálásakor hivatkozhat rá.

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

A telemetria és a tulajdonságok gyakran együtt működnek az eszközökről érkező adatforgalom kezelésére. Mivel az Azure Digital Twins-ba való belépés az [API](how-to-use-apis-sdks.md)-kon keresztül történik, általában a bemenők függvényt használja az eszközökről származó telemetria-vagy tulajdonság-események olvasására, és válaszként a ADT tulajdonságot kell beállítania. 

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

Ez a szakasz egy tipikus modellt tartalmaz, amely DTDL felületként íródott. A modell leírja a **bolygókat** , amelyek mindegyike névvel, tömeggel és hőmérséklettel rendelkezik.
 
Vegye figyelembe, hogy a **bolygók a** műholdakat is használhatják, és **krátereket** is tartalmazhatnak. Az alábbi példában a `Planet` modell két külső modellel (és) hivatkozik a többi entitáshoz való kapcsolódásra `Moon` `Crater` . Ezeket a modelleket az alábbi példában is meg kell határozni, de nagyon egyszerűek maradnak, hogy ne lehessen az elsődleges `Planet` példából kivonni.

```json
[
  {
    "@id": "dtmi:com:contoso:Planet;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "displayName": "Planet",
    "contents": [
      {
        "@type": "Property",
        "name": "name",
        "schema": "string"
      },
      {
        "@type": "Property",
        "name": "mass",
        "schema": "double"
      },
      {
        "@type": "Telemetry",
        "name": "Temperature",
        "schema": "double"
      },
      {
        "@type": "Relationship",
        "name": "satellites",
        "target": "dtmi:com:contoso:Moon;1"
      },
      {
        "@type": "Component",
        "name": "deepestCrater",
        "schema": "dtmi:com:contoso:Crater;1"
      }
    ]
  },
  {
    "@id": "dtmi:com:contoso:Crater;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:contoso:Moon;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2"
  }
]
```

A modell mezői a következők:

| Mező | Leírás |
| --- | --- |
| `@id` | A modell azonosítója. Formátumúnak kell lennie `dtmi:<domain>:<unique model identifier>;<model version number>` . |
| `@type` | A leírt információ típusát azonosítja. Illesztőfelület esetén a típus *illesztőfelület*. |
| `@context` | Beállítja a JSON-dokumentum [kontextusát](https://niem.github.io/json/reference/json-ld/context/) . A modelleknek használatban kell lenniük `dtmi:dtdl:context;2` . |
| `displayName` | választható Lehetővé teszi, hogy a modell felhasználóbarát nevet adjon, ha szükséges. |
| `contents` | Az összes többi illesztőfelület-adattal ide kerül, mint az attribútumok definícióinak tömbje. Minden attribútumnak `@type` ( *tulajdonság* , *telemetria* , *parancs* , *kapcsolat* vagy *összetevő* ) meg kell adnia az általa leírt illesztőfelület-információk, majd a tényleges attribútumot definiáló tulajdonságok készletét (például egy `name` `schema` *tulajdonság* definiálását). |

> [!NOTE]
> Vegye figyelembe, hogy az összetevő felülete (ebben a példában a *kráter* ) ugyanabban a tömbben van definiálva, mint az azt használó felület ( *Planet* ). Az összetevőket úgy kell meghatározni, hogy az API-hívások megtalálhatók legyenek az illesztőfelületen.

### <a name="possible-schemas"></a>Lehetséges sémák

A DTDL-hez hasonlóan a *tulajdonság* -és *telemetria* -attribútumok sémája szabványos primitív típusok –,, `integer` `double` `string` és `Boolean` – és más típusok (például `DateTime` és) lehetnek `Duration` . 

Az egyszerű típusok mellett a *Property* és a *telemetria* mezők a következő összetett típusokkal rendelkezhetnek:
* `Object`
* `Map`
* `Enum`

A *telemetria* mezők is támogatják a használatát `Array` .

### <a name="model-inheritance"></a>Modell öröklése

Időnként előfordulhat, hogy további modelleket szeretne specializálni. Előfordulhat *például, hogy egy általános modellel rendelkezik* , és speciális változatok *ConferenceRoom* és *edzőteremmel* rendelkezik. A specializáció kifejezéséhez a DTDL támogatja az öröklést: az illesztőfelületek örökölni tudnak egy vagy több másik felületről. 

Az alábbi példa a korábbi DTDL származó *bolygó* modelljét képzeli el újra egy nagyobb *CelestialBody* -modell altípusa szerint. A "Parent" modell először van definiálva, majd a "gyermek" modell a mező használatával épít rá `extends` .

```json
[
  {
    "@id": "dtmi:com:contoso:CelestialBody;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "displayName": "Celestial body",
    "contents": [
      {
        "@type": "Property",
        "name": "name",
        "schema": "string"
      },
      {
        "@type": "Property",
        "name": "mass",
        "schema": "double"
      },
      {
        "@type": "Telemetry",
        "name": "temperature",
        "schema": "double"
      }
    ]
  },
  {
    "@id": "dtmi:com:contoso:Planet;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "displayName": "Planet",
    "extends": "dtmi:com:contoso:CelestialBody;1",
    "contents": [
      {
        "@type": "Relationship",
        "name": "satellites",
        "target": "dtmi:com:contoso:Moon;1"
      },
      {
        "@type": "Component",
        "name": "deepestCrater",
        "schema": "dtmi:com:contoso:Crater;1"
      }
    ]
  },
  {
    "@id": "dtmi:com:contoso:Crater;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2"
  }
]
```

Ebben a példában a *CelestialBody* a *bolygó* nevét, tömegét és hőmérsékletét is segíti. A `extends` szakasz az illesztőfelület neve vagy az illesztőfelületek neveinek tömbje (ha szükséges, a több szülő modellből örökölheti a felületet).

Az öröklés alkalmazása után a kibővítési felület az összes tulajdonságot megjeleníti a teljes öröklési láncból.

A kiterjesztési felület nem változtathatja meg a szülő felületek definícióit; csak azok hozzáadására használható. Emellett nem tud újradefiniálni egy már definiált képességet a szülő felületén (még akkor is, ha a képességek definiálva vannak). Ha például egy szülő felület egy `double` tulajdonság *tömegét* határozza meg, akkor a kiterjesztési felület nem tartalmazhat *tömeges* deklarációt, még akkor is, ha az is a `double` .

## <a name="validating-models"></a>Modellek ellenőrzése

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="integrating-with-industry-standard-models"></a>Integrálás az iparági szabványnak megfelelő modellekkel

Az iparági szabványokon alapuló modellek használata vagy a standard ontológia-képviselet (például RDF vagy bagoly) használata gazdag kiindulási pontot biztosít az Azure digitális Twins-modelljeinek tervezésekor. Az iparági modellek használata a szabványosítás és az információmegosztás terén is segít.

Az Azure Digital Twins szolgáltatással való használathoz a modellnek a JSON-LD-alapú [**digitális Twins Definition Language (DTDL) nyelven**](concepts-models.md)kell szerepelnie. Ebből kifolyólag ez a cikk bemutatja, hogyan jelentheti az iparági szabványnak megfelelő modelleket a DTDL-ben, és integrálhatja a meglévő iparági fogalmakat DTDL szemantikaokkal, hogy az Azure digitális Twins használhassa őket. A DTDL modell ezután az igazság forrásaként szolgál a modellhez az Azure Digital Twins-n belül.

Az iparági szabványnak megfelelő modellek a DTDL való integrálásának két fő útja van:
* Ha még nem hozza létre a modelleket, megtervezheti azokat a **meglévő Starter DTDL-ontológiákat** , amelyek az iparágra jellemző nyelvet tartalmaznak.
* Ha már rendelkezik egy iparági szabványon alapuló meglévő modellel, a **DTDL konvertálnia kell őket** , hogy az Azure digitális Twins-ba kerüljön.

A két folyamattal kapcsolatos további információkért lásd [*: útmutató: az iparági szabványnak megfelelő modellek integrálása*](how-to-integrate-models.md).

## <a name="next-steps"></a>További lépések

Tekintse meg, hogyan kezelhetők a modellek a DigitalTwinModels API-kkal:
* [*Útmutató: egyéni modellek kezelése*](how-to-manage-model.md)

Vagy Ismerje meg, hogyan hozhatók létre a digitális ikrek a modellek alapján:
* [*Fogalmak: digitális ikrek és a Twin gráf*](concepts-twins-graph.md)


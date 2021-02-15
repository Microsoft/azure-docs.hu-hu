---
title: 'Funkciók: művelet és kontextus – személyre szabott'
titleSuffix: Azure Cognitive Services
description: A személyre szabott javaslatokat a testre szabható funkciók, a műveletek és a kontextus információi alapján végezheti el. A funkciók lehetnek általánosak vagy egy elemre jellemzőek.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/14/2019
ms.openlocfilehash: f49abd4ca1cc1ccdcb7ba2b0fab3bad953dede5d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100380542"
---
# <a name="features-are-information-about-actions-and-context"></a>A funkciók a műveletekkel és környezettel kapcsolatos információk

A személyre szabott szolgáltatás úgy működik, hogy megtanítja, hogy az alkalmazás hogyan jelenjen meg a felhasználók számára egy adott kontextusban.

A személyre szabott **funkciók** az **aktuális környezettel** kapcsolatos információkat használják, és kiválasztják a legjobb **műveletet**. A funkciók az összes olyan információt képviselik, amelyet úgy gondol, hogy a személyre szabhatja a magasabb szintű jutalmakat. A funkciók lehetnek általánosak vagy egy elemre jellemzőek. 

Előfordulhat például, hogy a következő **funkcióval** rendelkezik:

* A _felhasználó persona_ , például a `Sports_Shopper` . Ez nem lehet egyedi felhasználói azonosító. 
* A _tartalom_ , például ha egy videó az a `Documentary` , a `Movie` vagy a, `TV Series` vagy hogy elérhető-e egy kereskedelmi elem a tárolóban.
* Az _aktuális_ idő, például a hét napja.

A személyre szabott funkció nem írja elő, korlátozza vagy kijavítja a műveletekhez és környezetekhez elküldhető szolgáltatásokat:

* Ha nem rendelkezik ezekkel a funkciókkal, néhány művelethez elküldheti mások számára a szolgáltatásokat. Előfordulhat például, hogy a TV-sorozat attribútumai nem rendelkeznek.
* Előfordulhat, hogy néhány szolgáltatás csak időnként elérhető. Előfordulhat például, hogy egy mobil alkalmazás több információt biztosít, mint a weblap. 
* Idővel hozzáadhat és eltávolíthat szolgáltatásokat a környezettel és a műveletekkel kapcsolatban. A személyre szabás folytatja az elérhető információk megismerését.
* Legalább egy szolgáltatásnak szerepelnie kell a kontextusban. A személyre szabott nem támogatja az üres környezetet. Ha minden alkalommal csak rögzített kontextust küld, a személyre szabott művelet csak a műveletek funkcióit érintő rangsorolási műveletet fogja kiválasztani.
* A kategorikus funkciók esetében nem kell meghatároznia a lehetséges értékeket, és nem kell előre definiálni a numerikus értékek tartományait.

## <a name="supported-feature-types"></a>Támogatott szolgáltatások típusai

A személyre szabás a sztring, a numerikus és a logikai típusok funkcióit támogatja. Nagyon valószínű, hogy az alkalmazása általában a karakterlánc-funkciókat fogja használni, néhány kivétellel.

### <a name="how-choice-of-feature-type-affects-machine-learning-in-personalizer"></a>A szolgáltatás típusának kiválasztása a személyre szabás Machine Learningét érinti

* **Karakterláncok**: a sztringek esetében a kulcs és az érték minden kombinációja One-Hot szolgáltatásként kezelendő (pl. Műfaj: "ScienceFiction" és "műfaj": "dokumentumfilm", amely két új bemeneti funkciót hoz létre a Machine learning-modellhez.
* **Numerikus** értékek: numerikus értékeket kell használnia, ha a szám egy olyan magnitúdó, amely arányosan befolyásolhatja a személyre szabási eredményt. Ez a forgatókönyv függ. Egy egyszerűsített példában például a kiskereskedelmi élmény személyre szabása esetén a NumberOfPetsOwned olyan funkció lehet, amely numerikus, ha 2 vagy 3 személyre szabottan szeretné, hogy a megszemélyesítési eredmények kétszer vagy háromszor legyenek felhasználva, mint 1 kisállat. A numerikus egységeken alapuló, de a jelentés nem lineáris – például az életkor, a hőmérséklet vagy a személy magassága – a legmegfelelőbb karakterláncként kódolva. Például a DayOfMonth karakterlánc: "1", "2"... "31". Ha sok kategóriája van, akkor a funkciók minősége általában tartományok használatával javítható. Például a kor kódolása "Age": "0-5", "Age": "6-10" stb.
* A "false" értékkel ellátott **logikai** értékek úgy vannak megadva, mintha egyáltalán nem küldték el őket.

A nem jelen lévő funkciókat ki kell hagyni a kérelemből. Kerülje a funkciók NULL értékkel történő küldését, mert az a modell betanításakor a meglévőként és a "NULL" értékkel lesz feldolgozva.

## <a name="categorize-features-with-namespaces"></a>Funkciók kategorizálása névterekkel

A személyre szabás a névterekben rendezett funkciókat veszi igénybe. Ön határozza meg, hogy az alkalmazásban névterek vannak-e használatban, és mi legyen az. A névterek egy hasonló témakör vagy egy bizonyos forrásból származó funkciók csoportosítására szolgálnak.

Az alábbi példák az alkalmazások által használt szolgáltatások névtereit mutatják be:

* User_Profile_from_CRM
* Idő
* Mobile_Device_Info
* http_user_agent
* VideoResolution
* UserDeviceInfo
* Időjárás
* Product_Recommendation_Ratings
* current_time
* NewsArticle_TextAnalytics

A szolgáltatás névtereit a saját konvenciói szerint nevezheti el, feltéve, hogy érvényes JSON-kulcsok vannak. A névterek a funkciók különálló készletekre való rendszerezésére, valamint a hasonló névvel rendelkező funkciók egyértelműsítse szolgálnak. A névtereket "előtagként" tekintheti meg, amelyet a rendszer a szolgáltatás neveként ad hozzá. A névterek nem ágyazhatók egymásba.


A következő JSON-ban a, a `user` `state` és a `device` szolgáltatás névtereket tartalmaz. 

> [!Note]
> Jelenleg erősen ajánlott az UTF-8-alapú és más betűvel ellátott szolgáltatásbeli névterek neveinek használata. Például:, `user` , `state` és kezdje a következővel:, `device` `u` `s` és `d` . A jelenleg azonos első karakterrel rendelkező névterek ütközést okozhatnak a gépi tanuláshoz használt indexekben.

A JSON-objektumok tartalmazhatnak beágyazott JSON-objektumokat és egyszerű tulajdonságokat/értékeket is. Egy tömb csak akkor szerepelhet, ha a tömb elemei számokból állnak. 

```JSON
{
    "contextFeatures": [
        { 
            "user": {
                "profileType":"AnonymousUser",
                "latlong": ["47.6,-122.1"]
            }
        },
        {
            "environment": {
                "dayOfMonth": "28",
                "monthOfYear": "8",
                "timeOfDay": "13:00",
                "weather": "sunny"
            }
        },
        {
            "device": {
                "mobile":true,
                "Windows":true
            }
        },
        {
            "userActivity" : {
                "itemsInCart": 3,
                "cartValue": 250,
                "appliedCoupon": true
            }
        }
    ]
}
```

### <a name="restrictions-in-character-sets-for-namespaces"></a>A névterekhez tartozó karakterkészletek korlátozásai

A névtér elnevezéséhez használt karakterláncnak bizonyos korlátozásokat kell követnie: 
* Nem lehet Unicode.
* A névterek neveihez < 256 kóddal ellátott nyomtatható szimbólumokat használhat. 
* Nem használhat szimbólumokat < 32 (nem nyomtatható), 32 (szóköz), 58 (Colon), 124 (pipe) és 126 – 140 kóddal.
* Nem kezdődhet aláhúzással (_), vagy a szolgáltatás figyelmen kívül lesz hagyva.

## <a name="how-to-make-feature-sets-more-effective-for-personalizer"></a>A funkciók hatékonyabbvé tétele a személyre szabáshoz

Egy jó szolgáltatáskészlet segít személyre szabni, hogyan jósolhatja meg a legmagasabb jutalmat eredményező műveletet. 

Vegye fontolóra a funkciók küldését a személyre szabási rangsor API-ra, amely az alábbi ajánlásokat követi:

* A kategorikus és a karakterlánc típusú típusok olyan funkciókhoz használhatók, amelyek nem magnitúdók. 

* A személyre szabáshoz elegendő funkció áll rendelkezésre. Minél pontosabban célozza meg a tartalmat, annál több szolgáltatásra van szükség.

* Számos különféle *sűrűségű* funkció létezik. A szolgáltatás *sűrű* , ha sok elem van csoportosítva néhány gyűjtőn. Több ezer videó például "Long" (5 percnél hosszabb) és "Short" (5 percnél hosszabb) besorolású lehet. Ez egy *nagyon sűrű* funkció. Másfelől ugyanezen ezer elemnek egy "title" nevű attribútummal is rendelkezhet, amely szinte soha nem lesz ugyanaz az érték az egyik elemből a másikba. Ez egy nagyon nem sűrű vagy *ritka* funkció.  

A nagy sűrűségű funkciókkal a személyre szabott kikövetkeztethető az egyik elemről a másikra. Ha azonban csak néhány funkció van, és túl sűrű, a személyre szabott felhasználó megpróbál pontosan megcélozni a tartalmat, és csak néhány gyűjtő közül választhat.

### <a name="improve-feature-sets"></a>A szolgáltatási készletek fejlesztése 

Elemezheti a felhasználói viselkedést offline kiértékeléssel. Így megtekintheti a múltbeli adatmennyiségeket, és megtekintheti, hogy a funkciók jelentős mértékben járulnak hozzá a pozitív juttatásokhoz és a kevésbé jelentős előnyökhöz. Megtekintheti, hogy milyen funkciók segítik a segítséget, és hogy Ön és az alkalmazása is jobban megkeresheti a személyre szabott funkciókat, hogy még tovább javítsa az eredményeket.

A következő szakaszban gyakori eljárások találhatók a személyre szabott szolgáltatások fejlesztéséhez.

#### <a name="make-features-more-dense"></a>A funkciók sűrűvé tétele

Lehetőség van a szolgáltatáskészletek javítására úgy, hogy azok nagyobb vagy kevésbé sűrűvé teszik őket.

Például egy időbélyeg, amely a másodikra mutat, nagyon ritka funkció. Az idő a "Morning", a "délig", a "délutáni", illetve a "délelőtt" értékre való besorolásával sűrűbb (hatékony) lehet.

A tartózkodási hely adatai általában a szélesebb besorolások létrehozásának előnyeit is kihasználhatják. Például egy olyan Latitude-Longitude koordináta, mint például a lat: 47,67402 ° N, Long: 122,12154 ° W túl pontos, és a modell kényszeríti a szélesség és a hosszúság megtanulására különböző dimenzióként. Ha a tartózkodási hely adatai alapján próbál személyre szabni, a nagyobb szektorokban segít a tartózkodási hely adatainak csoportosításában. Ennek egyik egyszerű módja, ha kiválasztja a Lat-Long számok megfelelő kerekítési pontosságát, és a szélesség és hosszúság elemet a "területek" értékre kombinálva egyetlen sztringbe helyezi őket. Tegyük fel például, hogy egy jó módszer a 47,67402 ° N, a Long: 122,12154 ° s a régiókban, körülbelül néhány kilométer széles a "location": "34.3, 12,1".


#### <a name="expand-feature-sets-with-extrapolated-information"></a>A szolgáltatási készletek kibontása a kikövetkeztetett információkkal

További funkciókat is megtudhat a már meglévő információkból származtatott, nem feltárt attribútumok alapján. A kitalált filmek listájának személyre szabása például lehetséges, hogy egy hétvége és a hétköznap különböző viselkedést váltott ki a felhasználóktól? Az időt kiterjesztheti "hétvége" vagy "hétköznap" attribútummal. A nemzeti kulturális ünnepek bizonyos filmek típusaira vonatkozó figyelmet igényelnek? A "Halloween" attribútum például hasznos lehet olyan helyeken, ahol releváns. Lehetséges, hogy az esős Időjárás jelentős hatással van a sok ember számára a film megválasztására? Az idő és a hely esetében az időjárási szolgáltatás megadhatja ezt az információt, és további szolgáltatásként is hozzáadhatja. 

#### <a name="expand-feature-sets-with-artificial-intelligence-and-cognitive-services"></a>A szolgáltatás-készletek kibontása mesterséges intelligenciával és kognitív szolgáltatásokkal

A mesterséges intelligencia és a használatra kész Cognitive Services lehet a személyre szabott funkció. 

Az elemek mesterséges intelligencia-szolgáltatásokkal történő előfeldolgozásával automatikusan kinyerheti azokat az adatokat, amelyek valószínűleg relevánsak a személyre szabáshoz.

Például:

* A Movie-fájlok [video Indexer](https://azure.microsoft.com/services/media-services/video-indexer/) segítségével is futtathatók a jelenet elemeinek, szövegének, hangulatának és számos más attribútumnak a kinyeréséhez. Ezeket az attribútumokat ezután sűrűvé teheti az eredeti elemek metaadatainak nem megfelelő jellemzőinek megfelelően. 
* A képek az objektumok észlelése, az érzelmek és a hangulatok révén is futtathatók.
* A szövegben található információk kibonthatók az entitások, a hangulat, az entitások kibontása a Bing Knowledge Graph használatával stb.

Számos más [Azure-Cognitive Services](https://www.microsoft.com/cognitive-services)is használhat, például:

* [Entitáskapcsolás](../text-analytics/index.yml)
* [Text Analytics](../text-analytics/overview.md)
* [Érzelem](../face/overview.md)
* [Computer Vision](../computer-vision/overview.md)

## <a name="actions-represent-a-list-of-options"></a>A műveletek a lehetőségek listáját jelölik

Minden művelet:

* _Eseményazonosító_ . Ha már van eseményazonosító, küldje el a következőt:. Ha nem rendelkezik eseményazonosító, akkor ne küldjön egyet, a személyre szabott lehetőséggel létrehoz egyet, és visszaadja azt a rangsor kérésének válaszában. Az azonosító a Range eseményhez van társítva, nem a felhasználóhoz. Ha létrehoz egy azonosítót, a GUID a legjobban működik. 
* A funkciók listáját tartalmazza.
* A szolgáltatások listája nagy (száz) lehet, de javasoljuk, hogy a funkciók hatékonyságának értékelésével távolítsa el azokat a funkciókat, amelyek nem járulnak hozzá a jutalmak beszerzéséhez. 
* Előfordulhat, hogy a **műveletek** funkciói nem rendelkeznek a személyre szabott **környezet** által használt szolgáltatásokkal való korrelációval.
* A műveletek funkciói bizonyos műveletekben és másokban is előfordulhatnak. 
* Előfordulhat, hogy egy adott műveleti azonosító funkciói egy nap múlva elérhetők, de később elérhetetlenné válnak. 

A személyre szabott gépi tanulási algoritmusok jobb teljesítményt biztosítanak, ha a funkciók stabilak, de a rangsorolt hívások nem fognak sikerülni, ha a szolgáltatás beállított ideje módosul.

A műveletek rangsorolásakor ne küldjön több mint 50 műveletet. Ezek az 50-műveletek minden alkalommal megegyeznek, vagy megváltozhatnak. Ha például egy e-kereskedelmi alkalmazáshoz 10 000-es Termékkatalógus tartozik, akkor egy javaslat vagy egy szűrési motor használatával határozhatja meg, hogy 40 melyik ügyfelet szeretné megállapítani, és hogyan használhatja a személyre szabni a legtöbb jutalmat (például a felhasználó hozzáadja a kosárhoz) az aktuális környezethez.


### <a name="examples-of-actions"></a>Példák a műveletekre

A Rank API-nak küldött műveletek attól függnek, hogy mit próbál személyre szabni.

Íme néhány példa:

|Cél|Művelet|
|--|--|
|Személyre szabhatja, hogy mely cikkek legyenek kiemelve a hírek webhelyén.|Minden művelet egy lehetséges újságcikk.|
|Optimalizálja az ad-elhelyezést egy webhelyen.|Minden művelet elrendezést vagy szabályokat tartalmaz a hirdetések elrendezésének létrehozásához (például felül, a jobb oldalon, a kis képeken és a nagyméretű képeken).|
|Az ajánlott elemek személyre szabott rangsorolásának megjelenítése egy bevásárlási webhelyen.|Minden művelet egy adott termék.|
|Javasoljon olyan felhasználói felületi elemeket, mint például a szűrők, amelyek egy adott fényképre vonatkoznak.|Minden művelet lehet egy másik szűrő.|
|Válassza ki a csevegési bot válaszát a felhasználó szándékának tisztázására, vagy tegyen javaslatot egy műveletre.|Minden művelet a válasz értelmezésének egyik módja.|
|Válassza ki, mit szeretne megjeleníteni a keresési eredmények listájának tetején|Mindegyik művelet a leggyakoribb keresési eredmények egyike.|


### <a name="examples-of-features-for-actions"></a>Példák a műveletek funkcióihoz

A következő példák a műveletek funkcióinak megfelelő példáit mutatják be. Ezek sok minden alkalmazástól függenek.

* Funkciók a műveletek jellemzőivel. Például film vagy TV-sorozat?
* Azon funkciók, amelyekkel a felhasználók a múltban is felhasználhatták ezt a műveletet. Például ezt a filmet többnyire az A vagy B demográfiai személyek látják, általában csak egyszer játszanak egyszerre.
* *A felhasználók által* a műveletek végrehajtásának jellemzőit ismertető funkciók. Például a miniatűrben látható film posztere arcok, autók vagy tájak?

### <a name="load-actions-from-the-client-application"></a>Műveletek betöltése az ügyfélalkalmazás alapján

A műveletekből származó funkciók általában tartalomkezelő rendszerekből, katalógusokból és ajánló rendszerekből származnak. Az alkalmazás feladata a megfelelő adatbázisok és rendszerek műveleteire vonatkozó információk betöltése. Ha a műveletek nem változnak, vagy a betöltésük minden alkalommal szükségtelen hatással van a teljesítményre, akkor az alkalmazásban logika hozzáadásával gyorsítótárazhatja ezeket az információkat.

### <a name="prevent-actions-from-being-ranked"></a>A műveletek rangsorolásának megakadályozása

Bizonyos esetekben vannak olyan műveletek, amelyeket nem kíván megjeleníteni a felhasználók számára. A legjobb módszer arra, hogy megakadályozza, hogy egy művelet a legfelső szintnek legyen rangsorolva, és ne foglalja bele a rangsor API-ba az első helyen.

Bizonyos esetekben csak később határozható meg az üzleti logikában, ha egy Range API-hívást eredményező _művelet_ jelenik meg egy felhasználó számára. Ezekben az esetekben _inaktív eseményeket_ kell használnia.

## <a name="json-format-for-actions"></a>Műveletek JSON-formátuma

A rangsor meghívásakor több műveletet is el fog küldeni a következő lehetőségek közül:

A JSON-objektumok tartalmazhatnak beágyazott JSON-objektumokat és egyszerű tulajdonságokat/értékeket is. Egy tömb csak akkor szerepelhet, ha a tömb elemei számokból állnak. 

```json
{
    "actions": [
    {
      "id": "pasta",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "medium",
          "grams": [400,800]
        },
        {
          "nutritionLevel": 5,
          "cuisine": "italian"
        }
      ]
    },
    {
      "id": "ice cream",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none",
          "grams": [150, 300, 450]
        },
        {
          "nutritionalLevel": 2
        }
      ]
    },
    {
      "id": "juice",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none",
          "grams": [300, 600, 900]
        },
        {
          "nutritionLevel": 5
        },
        {
          "drink": true
        }
      ]
    },
    {
      "id": "salad",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "low",
          "grams": [300, 600]
        },
        {
          "nutritionLevel": 8
        }
      ]
    }
  ]
}
```

## <a name="examples-of-context-information"></a>Példák a környezeti információkra

A _kontextusra_ vonatkozó információk az egyes alkalmazásokról és a használati esetekről függenek, de általában olyan információkat is tartalmazhatnak, mint például a következők:

* A felhasználóval kapcsolatos demográfiai és profilbeli információk.
* HTTP-fejlécből kinyert információk, például felhasználói ügynök vagy HTTP-adatokból származtatott adatok, például az IP-címeken alapuló fordított földrajzi keresések.
* Az aktuális időpontra vonatkozó információk, például a hét napja, hétvége vagy nem, reggel vagy délután, ünnepi időszak vagy nem stb.
* A mobil alkalmazásokból kinyert információk, például a hely, a mozgás vagy az akkumulátor szintje.
* A felhasználók viselkedésének múltbeli összesítései – például hogy milyen filmes műfajok jelennek meg a felhasználó számára.

Az alkalmazás feladata, hogy betöltse a környezetre vonatkozó információkat a megfelelő adatbázisokból, érzékelőkből és rendszerekből. Ha a környezeti adatok nem változnak, az alkalmazásban logika hozzáadásával gyorsítótárazhatja ezeket az adatokat, mielőtt elküldené a rangsor API-nak.

## <a name="json-format-for-context"></a>JSON formátum a kontextushoz 

A kontextus a Rank API-nak eljuttatott JSON-objektumként van kifejezve:

A JSON-objektumok tartalmazhatnak beágyazott JSON-objektumokat és egyszerű tulajdonságokat/értékeket is. Egy tömb csak akkor szerepelhet, ha a tömb elemei számokból állnak. 

```JSON
{
    "contextFeatures": [
        { 
            "user": {
                "name":"Doug"
            }
        },
        {
            "state": {
                "timeOfDay": "noon",
                "weather": "sunny"
            }
        },
        {
            "device": {
                "mobile":true,
                "Windows":true,
                "screensize": [1680,1050]
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Következő lépések

[Megerősítő tanulás](concepts-reinforcement-learning.md)

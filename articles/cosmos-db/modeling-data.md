---
title: Az adatmodellezés Azure Cosmos DB
titleSuffix: Azure Cosmos DB
description: Ismerje meg a NoSQL-adatbázisokban található adatmodellezést, a kapcsolati adatbázis és a dokumentum-adatbázis modellezési adatainak különbségeit.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: a141177846def9c94216684c1083d0d336eeda1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93333240"
---
# <a name="data-modeling-in-azure-cosmos-db"></a>Adatmodellezés Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A séma nélküli adatbázisok (például a Azure Cosmos DB) rendkívül egyszerűvé teszik a strukturálatlan és részben strukturált adatmennyiségek tárolását és lekérdezését, némi időt kell fordítania az adatmodellre, hogy a teljesítmény és a méretezhetőség, valamint a legalacsonyabb költségek alapján a szolgáltatás a lehető legtöbbet kapja.

Hogyan történik az adattárolás tárolása? Hogyan fogja az alkalmazás lekérdezni és lekérdezni az adatait? Az alkalmazás olvasása nagy vagy írható-e?

A cikk elolvasása után a következő kérdésekre tud válaszolni:

* Mi az az adatmodellezés, és miért érdemes érdekelni?
* Miben különböznek a modellezési adatAzure Cosmos DBek a kapcsolódó adatbázisokban?
* Nem relációs adatbázisban Hogyan expressz adatkapcsolatot?
* Mikor tudom beágyazni az adatbeágyazást, és mikor kell csatolni az adatkapcsolatot?

## <a name="embedding-data"></a>Az adatbeágyazás

Amikor megkezdi az adatok modellezését Azure Cosmos DB próbálja meg kezelni az entitásokat JSON-dokumentumként ábrázolt **önálló elemekként** .

Az összehasonlításhoz először lássuk, hogyan lehet egy relációs adatbázisban modellezni az adatmodellt. Az alábbi példa azt szemlélteti, hogy egy személy hogyan tárolható egy kapcsolódó adatbázisban.

:::image type="content" source="./media/sql-api-modeling-data/relational-data-model.png" alt-text="Rokon adatbázis-modell" border="false":::

A kapcsolati adatbázisok használatakor a stratégia az összes adathalmaz normalizálása. Az adatnormalizálás általában magában foglalja egy entitás, például egy személy bevonását és a különálló összetevőkbe való lebontását. A fenti példában egy személy több kapcsolattartási adattal és több rekorddal is rendelkezhet. A kapcsolattartási adatok tovább bonthatók a gyakori mezők (például egy típus) további kibontásával. Ugyanez vonatkozik a címekre, az egyes rekordok lehetnek *otthoni* vagy *üzleti* típusúak.

Az adatok normalizálása során a Guiding előfeltétel, hogy **elkerülje a redundáns adatok tárolását** az egyes rekordokon, és inkább az adatokra hivatkozzon. Ebben a példában egy személy olvasásához, az összes kapcsolattartási adataival és címével együtt kell használnia az adatokat az adatoknak a futási időben való visszaírásához (vagy denormalizálása).

```sql
SELECT p.FirstName, p.LastName, a.City, cd.Detail
FROM Person p
JOIN ContactDetail cd ON cd.PersonId = p.Id
JOIN ContactDetailType cdt ON cdt.Id = cd.TypeId
JOIN Address a ON a.PersonId = p.Id
```

Egyetlen személy frissítése a kapcsolattartási adatokkal és a címekkel több különböző tábla írási műveleteire van szükség.

Most vessünk egy pillantást arra, hogyan modellezjük ugyanazokat az adategységeket Azure Cosmos DBban.

```json
{
    "id": "1",
    "firstName": "Thomas",
    "lastName": "Andersen",
    "addresses": [
        {
            "line1": "100 Some Street",
            "line2": "Unit 1",
            "city": "Seattle",
            "state": "WA",
            "zip": 98012
        }
    ],
    "contactDetails": [
        {"email": "thomas@andersen.com"},
        {"phone": "+1 555 555-5555", "extension": 5555}
    ]
}
```

A fenti módszer használatával a személy rekordját **denormalizáljuk** a személyhez kapcsolódó összes információ, például a kapcsolattartási adatok és a címek *egyetlen JSON* -dokumentumba való **beágyazásával** .
Továbbá, mivel nem korlátozódik a rögzített sémára, rugalmasan elvégezheti a különböző formájú adatok elérhetőségét.

Egy teljes person rekordnak az adatbázisból való beolvasása már **egyetlen olvasási művelet** egyetlen tárolón és egyetlen elemnél. Egy személy rekordjának frissítése, a kapcsolattartási adatokkal és a címekkel együtt egyetlen, egyetlen elemre vonatkozó **írási művelet** is.

Az adatok denormalizálása esetén előfordulhat, hogy az alkalmazásnak kevesebb lekérdezést és frissítést kell kiállítania a gyakori műveletek végrehajtásához.

### <a name="when-to-embed"></a>Mikor ágyazható be

Általánosságban elmondható, hogy a következőket használja beágyazott adatmodellek esetén:

* **Az entitások között találhatók kapcsolatok** .
* Az entitások között **egy-az-egyhez** kapcsolat áll fenn.
* A beágyazott adatértékek **ritkán változnak**.
* Olyan beágyazott adat is van, amely nem növekszik **kötés nélkül**.
* Vannak olyan beágyazott adathalmazok, amelyek **gyakran vannak lekérdezve**.

> [!NOTE]
> Általában a denormalizált adatmodellek jobb **olvasási** teljesítményt nyújtanak.

### <a name="when-not-to-embed"></a>Ha nincs beágyazva

Habár a Azure Cosmos DBi szabálya az, hogy denormalizálja a mindent, és egyetlen elembe ágyazza be az összes adathalmazt, ez bizonyos helyzetek elkerülését eredményezheti.

Használja ezt a JSON-kódrészletet.

```json
{
    "id": "1",
    "name": "What's new in the coolest Cloud",
    "summary": "A blog post by someone real famous",
    "comments": [
        {"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
        {"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
        …
        {"id": 100001, "author": "jane", "comment": "and on we go ..."},
        …
        {"id": 1000000001, "author": "angry", "comment": "blah angry blah angry"},
        …
        {"id": ∞ + 1, "author": "bored", "comment": "oh man, will this ever end?"},
    ]
}
```

Előfordulhat, hogy a beágyazott megjegyzésekkel rendelkező post entitások úgy néznek ki, mintha egy tipikus blogot vagy egy CMS-t modelleztek. Ezzel a példával a megjegyzések tömbje **nincs korlátozva, ami** azt jelenti, hogy a Hozzászólások száma nem megengedett. Ez problémát jelenthet, mivel az elemek mérete végtelenül nagy növekedést eredményezhet.

Ahogy az elem mérete növekszik, az adatokat a vezetékes hálózaton keresztül továbbíthatja, valamint az elem olvasását és frissítését is befolyásolja.

Ebben az esetben jobb lenne a következő adatmodellt figyelembe venni.

```json
Post item:
{
    "id": "1",
    "name": "What's new in the coolest Cloud",
    "summary": "A blog post by someone real famous",
    "recentComments": [
        {"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
        {"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
        {"id": 3, "author": "jane", "comment": "....."}
    ]
}

Comment items:
{
    "postId": "1"
    "comments": [
        {"id": 4, "author": "anon", "comment": "more goodness"},
        {"id": 5, "author": "bob", "comment": "tails from the field"},
        ...
        {"id": 99, "author": "angry", "comment": "blah angry blah angry"}
    ]
},
{
    "postId": "1"
    "comments": [
        {"id": 100, "author": "anon", "comment": "yet more"},
        ...
        {"id": 199, "author": "bored", "comment": "will this ever end?"}
    ]
}
```

Ez a modell a post tárolóban beágyazott három legújabb megjegyzést tartalmaz, amely az attribútumok rögzített készletét tartalmazó tömb. A többi Megjegyzés a 100-es hozzászólások kötegei között van csoportosítva, és külön elemként van tárolva. A köteg mérete 100, mert a fiktív alkalmazás lehetővé teszi, hogy a felhasználó egyszerre töltsön be 100-megjegyzéseket.  

Egy másik eset, amikor az adatok beágyazása nem jó ötlet, ha a beágyazott adatokat gyakran használják az elemek között, és gyakran változnak.

Használja ezt a JSON-kódrészletet.

```json
{
    "id": "1",
    "firstName": "Thomas",
    "lastName": "Andersen",
    "holdings": [
        {
            "numberHeld": 100,
            "stock": { "symbol": "zaza", "open": 1, "high": 2, "low": 0.5 }
        },
        {
            "numberHeld": 50,
            "stock": { "symbol": "xcxc", "open": 89, "high": 93.24, "low": 88.87 }
        }
    ]
}
```

Ez jelenthet egy személy értékpapír-portfólióját. Úgy döntöttünk, hogy az összes portfólió-dokumentumba ágyazza be a tőzsdei adatokat. Olyan környezetben, ahol gyakran változnak a kapcsolódó adatforgalom, például tőzsdei kereskedési alkalmazás, a gyakran módosult adatbeágyazási szolgáltatás azt jelenti, hogy minden egyes portfólió-dokumentum folyamatosan frissül, amikor egy készletet kereskednek.

A *tőzsdei* a készlet több százszor is kereskedhető egyetlen nap alatt, és több ezer felhasználó *is rendelkezhet a* saját portfólióján. Egy adatmodellel, például a fentiekhez hasonlóan több ezer portfóliót is frissíteni kell, hogy minden nap sokszor olyan rendszerhez vezetett, amely nem fog jól méretezhető.

## <a name="referencing-data"></a>Hivatkozó adatértékek

Az adatok beágyazásával szépen működik sok esetben, de az adatok denormalizálása során több problémát is okozhat, mint amennyit érdemes. Mit teszünk most?

A relációs adatbázisok nem az egyetlen hely, ahol kapcsolatokat hozhat létre az entitások között. A dokumentum-adatbázisokban egy olyan dokumentumban található információ, amely más dokumentumokban lévő adatokra vonatkozik. Nem javasoljuk olyan rendszerek kialakítását, amelyek jobban illeszkednek a Azure Cosmos DB vagy bármely más dokumentum-adatbázis relációs adatbázisához, de az egyszerű kapcsolatok is hasznosak lehetnek.

Az alábbi JSON-ben úgy döntöttünk, hogy egy korábbi, de ezúttal a portfólióban található készletre hivatkozunk, a beágyazás helyett. Ily módon, amikor a tőzsdei elem gyakran változik a nap folyamán, az egyetlen dokumentum, amelyet frissíteni kell, az egyetlen Stock-dokumentum.

```json
Person document:
{
    "id": "1",
    "firstName": "Thomas",
    "lastName": "Andersen",
    "holdings": [
        { "numberHeld":  100, "stockId": 1},
        { "numberHeld":  50, "stockId": 2}
    ]
}

Stock documents:
{
    "id": "1",
    "symbol": "zaza",
    "open": 1,
    "high": 2,
    "low": 0.5,
    "vol": 11970000,
    "mkt-cap": 42000000,
    "pe": 5.89
},
{
    "id": "2",
    "symbol": "xcxc",
    "open": 89,
    "high": 93.24,
    "low": 88.87,
    "vol": 2970200,
    "mkt-cap": 1005000,
    "pe": 75.82
}
```

Ennek a megközelítésnek a közvetlen hátránya azonban az, ha az alkalmazásnak meg kell jelenítenie a személy portfóliójának megjelenítésekor tárolt egyes készletek információit; Ebben az esetben több utazást kell elvégeznie az adatbázisba az egyes dokumentumok adatainak betöltéséhez. Itt döntöttünk, hogy javítsuk az írási műveletek hatékonyságát, amelyek a nap folyamán gyakran történnek, de az olyan olvasási műveletekkel szemben, amelyek esetleg kevésbé érintik az adott rendszer teljesítményét.

> [!NOTE]
> A normalizált adatmodellek **esetében több oda-és visszaút szükséges** a kiszolgálónak.

### <a name="what-about-foreign-keys"></a>Mi a helyzet a külső kulcsokkal?

Mivel jelenleg nem áll rendelkezésre a korlátozás, a külső kulcs vagy más, a dokumentumokban található, a dokumentumok közötti kapcsolatok gyakorlatilag "gyenge hivatkozások", és az adatbázis nem fogja ellenőrizni. Ha biztosítani szeretné, hogy egy dokumentum valóban létezik-e, ezt az alkalmazásban kell megtennie, vagy a kiszolgálóoldali eseményindítók vagy a tárolt eljárások használatával Azure Cosmos DBon.

### <a name="when-to-reference"></a>Mikor hivatkozzon

Általánosságban a normalizált adatmodellek használata a következőket eredményezi:

* **Egy-a-többhöz** kapcsolatot jelképez.
* **Több-a-többhöz** kapcsolatot jelképez.
* A kapcsolódó **adatváltozások gyakran változnak**.
* A hivatkozott adat nem **köthető**.

> [!NOTE]
> A normalizálás általában jobb **írási** teljesítményt nyújt.

### <a name="where-do-i-put-the-relationship"></a>Hová helyezem a kapcsolatot?

A kapcsolat növekedése segít meghatározni, hogy mely dokumentum tárolja a hivatkozást.

Ha megtekintjük az alábbi JSON-t, amely a modellek közzétevőit és könyveit mutatja be.

```json
Publisher document:
{
    "id": "mspress",
    "name": "Microsoft Press",
    "books": [ 1, 2, 3, ..., 100, ..., 1000]
}

Book documents:
{"id": "1", "name": "Azure Cosmos DB 101" }
{"id": "2", "name": "Azure Cosmos DB for RDBMS Users" }
{"id": "3", "name": "Taking over the world one JSON doc at a time" }
...
{"id": "100", "name": "Learn about Azure Cosmos DB" }
...
{"id": "1000", "name": "Deep Dive into Azure Cosmos DB" }
```

Ha a Publisherben lévő könyvek száma kis mértékben növekszik, a könyv hivatkozása a kiadói dokumentumon belül is hasznos lehet. Ha azonban a telefonkönyvek száma nincs lekötve, akkor ez az adatmodell változékony, növekvő tömböket eredményezhet, ahogy a fenti példában a közzétevői dokumentum is.

A dolgok egy kicsit való váltás egy olyan modellt eredményez, amely továbbra is ugyanazt az adatmennyiséget jelöli, de most elkerüli ezeket a nagy mértékben változékony gyűjteményeket.

```json
Publisher document:
{
    "id": "mspress",
    "name": "Microsoft Press"
}

Book documents:
{"id": "1","name": "Azure Cosmos DB 101", "pub-id": "mspress"}
{"id": "2","name": "Azure Cosmos DB for RDBMS Users", "pub-id": "mspress"}
{"id": "3","name": "Taking over the world one JSON doc at a time"}
...
{"id": "100","name": "Learn about Azure Cosmos DB", "pub-id": "mspress"}
...
{"id": "1000","name": "Deep Dive into Azure Cosmos DB", "pub-id": "mspress"}
```

A fenti példában eldobta a nem kötött gyűjteményt a közzétevői dokumentumon. Ehelyett a közzétevőre mutató hivatkozást adunk meg minden könyv dokumentumon.

### <a name="how-do-i-model-manymany-relationships"></a>Hogyan modell: sok kapcsolat?

Egy relációs adatbázisban *: sok* kapcsolat gyakran az illesztési táblázatokkal van modellezve, ami csak a többi táblázat rekordjait egyesíti.


:::image type="content" source="./media/sql-api-modeling-data/join-table.png" alt-text="Táblák csatlakoztatása" border="false":::

Lehet, hogy megkísértette ugyanazt a dolgot a dokumentumok használatával, és olyan adatmodellt hoz létre, amely a következőhöz hasonlóan néz ki.

```json
Author documents:
{"id": "a1", "name": "Thomas Andersen" }
{"id": "a2", "name": "William Wakefield" }

Book documents:
{"id": "b1", "name": "Azure Cosmos DB 101" }
{"id": "b2", "name": "Azure Cosmos DB for RDBMS Users" }
{"id": "b3", "name": "Taking over the world one JSON doc at a time" }
{"id": "b4", "name": "Learn about Azure Cosmos DB" }
{"id": "b5", "name": "Deep Dive into Azure Cosmos DB" }

Joining documents:
{"authorId": "a1", "bookId": "b1" }
{"authorId": "a2", "bookId": "b1" }
{"authorId": "a1", "bookId": "b2" }
{"authorId": "a1", "bookId": "b3" }
```

Ez működne. Ha azonban betölti a könyveket, vagy betölt egy könyvet a szerzővel, mindig legalább két további lekérdezést kell megkövetelnie az adatbázison. Egy lekérdezés az összekapcsolási dokumentumhoz, majd egy másik lekérdezés, amely beolvassa a tényleges dokumentumot a csatlakozáshoz.

Ha ez az összekapcsolási táblázat két adat összeragasztását végzi, akkor miért nem dobja el teljesen a műveletet?
Vegye figyelembe a következőket.

```json
Author documents:
{"id": "a1", "name": "Thomas Andersen", "books": ["b1", "b2", "b3"]}
{"id": "a2", "name": "William Wakefield", "books": ["b1", "b4"]}

Book documents:
{"id": "b1", "name": "Azure Cosmos DB 101", "authors": ["a1", "a2"]}
{"id": "b2", "name": "Azure Cosmos DB for RDBMS Users", "authors": ["a1"]}
{"id": "b3", "name": "Learn about Azure Cosmos DB", "authors": ["a1"]}
{"id": "b4", "name": "Deep Dive into Azure Cosmos DB", "authors": ["a2"]}
```

Ha már megismertem a szerzőt, azonnal tudni szeretném, hogy mely könyveket írtak, és fordítva, ha betöltöttem a könyv dokumentumát, a szerző (k) azonosítóit szeretném megismerni. Ezzel elmenti a köztes lekérdezést az illesztési táblázaton, így csökkentve az alkalmazás által elvégezhető kiszolgáló-átutazások számát.

## <a name="hybrid-data-models"></a>Hibrid adatmodellek

Most már megtekintette a beágyazást (vagy a denormalizálás) és a viszonyítási (vagy normalizáló) adatfeldolgozást, és mindegyiknek megvan a saját oldala.

Nem mindig kell sem lennie, se nem kell megijedni, hogy egy kicsit felkeverje a dolgokat.

Az alkalmazás konkrét használati mintái és számítási feladatain alapuló esetekben előfordulhat, hogy a beágyazott és a hivatkozott adatok keverése is ésszerű, és egyszerűbb alkalmazás-logikát eredményezhet, kevesebb kiszolgálóval, miközben továbbra is jó teljesítményt tart fenn.

Vegye figyelembe a következő JSON-t.

```json
Author documents:
{
    "id": "a1",
    "firstName": "Thomas",
    "lastName": "Andersen",
    "countOfBooks": 3,
    "books": ["b1", "b2", "b3"],
    "images": [
        {"thumbnail": "https://....png"}
        {"profile": "https://....png"}
        {"large": "https://....png"}
    ]
},
{
    "id": "a2",
    "firstName": "William",
    "lastName": "Wakefield",
    "countOfBooks": 1,
    "books": ["b1"],
    "images": [
        {"thumbnail": "https://....png"}
    ]
}

Book documents:
{
    "id": "b1",
    "name": "Azure Cosmos DB 101",
    "authors": [
        {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "https://....png"},
        {"id": "a2", "name": "William Wakefield", "thumbnailUrl": "https://....png"}
    ]
},
{
    "id": "b2",
    "name": "Azure Cosmos DB for RDBMS Users",
    "authors": [
        {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "https://....png"},
    ]
}
```

Itt (főleg) követte a beágyazott modellt, ahol a más entitásokból származó adatok a legfelső szintű dokumentumba vannak beágyazva, de más adatok is hivatkoznak rá.

Ha megtekinti a könyv dokumentumát, néhány érdekes mezőt láthatunk, amikor megtekintjük a szerzők tömbjét. Létezik egy olyan mező, `id` amely az a mező, amellyel visszahivatkozhatunk egy szerzői dokumentumra, egy normalizált modell standard gyakorlatára, de a és a is `name` `thumbnailUrl` . Megakadt az `id` alkalmazásba, és kihagytam az alkalmazást, hogy a megfelelő szerzői dokumentumhoz szükséges további információkat szerezzen a "link" használatával, de mivel az alkalmazás megjeleníti a szerző nevét és egy miniatűr képet minden egyes könyvnél, a szerzőn belüli **egyes** adatok denormalizálása révén a listában elmenthető egy oda-vissza.

Győződjön meg arról, hogy ha a szerző neve megváltozott, vagy frissíteni szeretné a fényképét, akkor azt a feltételezés alapján, hogy a szerzők gyakran nem változtatják meg a nevüket, ez egy elfogadható tervezési döntés.  

A példában vannak **előre kiszámított összesítési** értékek, amelyek a költséges feldolgozást mentik egy olvasási műveletre. A példában a szerzői dokumentumba beágyazott egyes adatmennyiségek a futásidőben kiszámított adatértékek. Minden alkalommal, amikor új könyvet tesznek közzé, létrejön egy könyv **-** dokumentum, és a countOfBooks mező az adott szerző számára létező könyv-dokumentumok száma alapján számított értékre van állítva. Ez az optimalizálás hasznos lehet az olvasási nehéz rendszerekben, ahol a beolvasások optimalizálása érdekében az írásokra vonatkozó számításokat tehetünk.

Az előre kiszámított mezőket tartalmazó modell lehetővé teszi, hogy Azure Cosmos DB támogassa a **többdokumentumos tranzakciókat**. Számos NoSQL-tároló nem tud tranzakciókat felvenni a dokumentumok között, ezért a tervezési döntések, például a "minden esetben beágyazása" lehetőséget a korlátozás miatt. A Azure Cosmos DB használatával kiszolgálóoldali eseményindítókat vagy tárolt eljárásokat is használhat, amelyek a könyvek beszúrását és a szerzők frissítését mind egy savas tranzakción belül. Most nem **kell** semmit beágyaznia egyetlen dokumentumba, hogy az adatai konzisztensek maradnak.

## <a name="distinguishing-between-different-document-types"></a>Különböző dokumentumtípusok megkülönböztetése

Bizonyos esetekben előfordulhat, hogy különböző dokumentumtípust szeretne összekeverni ugyanabban a gyűjteményben; Ez általában akkor történik, ha több, kapcsolódó dokumentumot is szeretne ugyanazon a [partíción](partitioning-overview.md)ülni. Tegyük fel például, hogy a könyvek és a könyv-felülvizsgálatok ugyanabban a gyűjteményben vannak, és particionálja a következővel: `bookId` . Ilyen esetben általában fel kell vennie a dokumentumaiba egy olyan mezővel, amely azonosítja a típusát, hogy megkülönböztesse őket.

```json
Book documents:
{
    "id": "b1",
    "name": "Azure Cosmos DB 101",
    "bookId": "b1",
    "type": "book"
}

Review documents:
{
    "id": "r1",
    "content": "This book is awesome",
    "bookId": "b1",
    "type": "review"
},
{
    "id": "r2",
    "content": "Best book ever!",
    "bookId": "b1",
    "type": "review"
}
```

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatja, hogy az adatmodellezés egy séma nélküli világban annyira fontos, mint valaha.

Ugyanúgy, ahogy egyetlen módon nem lehet a képernyőn egy adathalmazt képviselni, az adatai modellezése nem történik meg egyetlen módon. Ismernie kell az alkalmazást, és azt, hogy miként fogja létrehozni, felhasználni és feldolgozni az adatfeldolgozást. Ezután az itt bemutatott irányelvek némelyikével megadhatja, hogyan hozhat létre olyan modellt, amely az alkalmazás azonnali szükségleteit kezeli. Az alkalmazások módosítását követően kihasználhatja a séma nélküli adatbázisok rugalmasságát, és így egyszerűen áttekintheti az adatmodellt.

Ha többet szeretne megtudni a Azure Cosmos DBről, tekintse meg a szolgáltatás [dokumentációs](https://azure.microsoft.com/documentation/services/cosmos-db/) oldalát.

Ha meg szeretné tudni, hogyan osztható fel az adatai több partícióra, tekintse meg az [Adatparticionálást Azure Cosmos DBban](partitioning-overview.md).

Ha meg szeretné tudni, hogyan modellezheti és particionálhatja Azure Cosmos DB a valós példa használatával, tekintse át az [ adatmodellezés és particionálás – egy Real-World példát](how-to-model-partition-example.md).

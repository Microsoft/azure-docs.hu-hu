---
title: A lekérdezés kiterjesztésének szinonimái a keresési indexben
titleSuffix: Azure Cognitive Search
description: Hozzon létre egy szinonimát a keresési lekérdezés hatókörének kibontásához egy Azure Cognitive Search indexen. A hatókör ki van bővítve, hogy belefoglalja a listában megadott egyenértékű feltételeket.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/18/2020
ms.openlocfilehash: 5e608d38ff70d51b569088629a6d80cb08e74ed4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98251624"
---
# <a name="synonyms-in-azure-cognitive-search"></a>Az Azure Cognitive Search szinonimái

A szinonima leképezésekkel egyenértékű kifejezéseket társíthat, és kiterjesztheti egy lekérdezés hatókörét anélkül, hogy a felhasználónak ténylegesen meg kellene adnia a kifejezést. Tegyük fel például, hogy a "Dog", a "kutya" és a "Puppy" szinonimákat tartalmaz, a "kutya" lekérdezése egy "Dog" tartalmú dokumentumnak felel meg.

## <a name="create-synonyms"></a>Szinonimák létrehozása

A szinonimák leképezése egy olyan eszköz, amely egyszer hozható létre, és számos index által használható. A [szolgáltatási szint](search-limits-quotas-capacity.md#synonym-limits) határozza meg, hogy hány szinonima hozható létre, amelyek három szinonimát mutatnak az ingyenes és alapszintű csomagokra vonatkozóan, akár 20 a standard szintig. 

Több szinonima-térképet is létrehozhat különböző nyelvekhez, például angol és francia verziókhoz, vagy a lexikonokhoz, ha a tartalom technikai vagy homályos terminológiát tartalmaz. Bár a keresési szolgáltatásban több szinonima-leképezés is létrehozható, egy mező csak az egyiket használhatja.

A szinonima-Térkép olyan nevet, formátumot és szabályokat tartalmaz, amelyek szinonimául szolgáló leképezési bejegyzésként működnek. Az egyetlen támogatott formátum `solr` , a formátum pedig meghatározza a `solr` szabályok kialakítását.

```http
POST /synonymmaps?api-version=2020-06-30
{
    "name": "geo-synonyms",
    "format": "solr",
    "synonyms": "
        USA, United States, United States of America\n
        Washington, Wash., WA => WA\n"
}
```

Szinonimák leképezésének létrehozásához használja a [szinonimák létrehozása (REST API)](/rest/api/searchservice/create-synonym-map) vagy egy Azure SDK-t. A C# fejlesztők számára javasolt a [szinonimák hozzáadása az Azure-beli kognitív kereséshez a c# használatával](search-synonyms-tutorial-sdk.md).

## <a name="define-rules"></a>Szabályok definiálása

A leképezési szabályok megfelelnek az Apache Solr nyílt forráskódú szinonimájának, amely az alábbi dokumentumban olvasható: [SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter). A `solr` Formátum két típusú szabályt támogat:

+ egyenértékűség (ahol a feltételek a lekérdezésben egyenlő helyettesítők)

+ explicit leképezések (ahol a feltételek a lekérdezés előtt egy explicit kifejezésre vannak leképezve)

Minden szabályt az új sor karakterének () kell elválasztani `\n` . Egy ingyenes szolgáltatásban legfeljebb 5 000 szabályt adhat meg, és a más rétegekben a 20 000 szabályok alapján. Minden szabály legfeljebb 20 bővítést tartalmazhat (vagy egy szabály elemeit). További információ: [szinonima korlátai](search-limits-quotas-capacity.md#synonym-limits).

A lekérdezés-elemzők kis-és nagybetűket is tartalmazhatnak, de ha a karakterláncban szeretné megőrizni a speciális karaktereket, például egy vesszőt vagy kötőjelet, adja hozzá a megfelelő Escape-karaktereket a szinonimák leképezésének létrehozásakor.

### <a name="equivalency-rules"></a>Egyenértékűségi szabályok

Az egyenértékű feltételekhez tartozó szabályok vesszővel vannak elválasztva ugyanazon a szabályon belül. Az első példában a lekérdezés a következőre `USA` lesz kibontva: `USA` vagy `"United States"` vagy `"United States of America"` . Figyelje meg, hogy ha meg szeretné egyezni egy kifejezéssel, a lekérdezésnek idézőjelek közé kell esnie.

Az egyenértékűségi esetben a lekérdezés `dog` kibontja a lekérdezést, és a következőt is tartalmazza: `puppy` és `canine` .

```json
{
"format": "solr",
"synonyms": "
    USA, United States, United States of America\n
    dog, puppy, canine\n
    coffee, latte, cup of joe, java\n"
}
```

### <a name="explicit-mapping"></a>Explicit leképezés

Az explicit hozzárendelések szabályait egy nyíl jelöli `=>` . Ha meg van adva, a bal oldali keresési lekérdezés egy lejárati sorozata a jobb oldalon a `=>` lekérdezés időpontjában lévő alternatívákkal lesz lecserélve.

A explicit esetben a (z) vagy a (z) lekérdezése a (z `Washington` `Wash.` `WA` `WA` ) és a (z), és a lekérdezési motor csak a kifejezésben szereplő egyezéseket fogja keresni `WA` . A explicit leképezés csak a megadott irányra vonatkozik, és nem írja át újra a lekérdezést ebben `WA` `Washington` az esetben.

```json
{
"format": "solr",
"synonyms": "
    Washington, Wash., WA => WA\n
    California, Calif., CA => CA\n"
}
```

### <a name="escaping-special-characters"></a>Speciális karakterek megmenekülése

A szinonimákat a lekérdezés feldolgozása során elemezzük. Ha vesszőket vagy más speciális karaktereket tartalmazó szinonimákat kell meghatároznia, akkor elkerülheti őket egy fordított perjeltel, például a következő példához hasonló módon:

```json
{
"format": "solr",
"synonyms": "WA\, USA, WA, Washington\n"
}
```

Mivel a fordított perjel önmagában más nyelveken, például a JSON-ban és a C#-ban is különleges karakter, valószínűleg kétszer kell elmenekülnie. Például a fenti szinonima-Térkép REST API eljuttatott JSON a következőképpen fog kinézni:

```json
{
"format":"solr",
"synonyms": "WA\\, USA, WA, Washington"
}
```

## <a name="upload-and-manage-synonym-maps"></a>Szinonimák leképezésének feltöltése és kezelése

Ahogy azt korábban említettük, a lekérdezési és indexelési feladatok megszakítása nélkül hozhat létre vagy frissíthet szinonimákat. A szinonimák egy különálló objektumok (például az indexek vagy az adatforrások), és mindaddig, amíg egyetlen mező sem használja azt, a frissítések nem okozzák az indexelést vagy a lekérdezéseket. Ha azonban egy szinonimát hoz létre egy mező-definícióhoz, és egy szinonimát is töröl, a kérdéses mezőket tartalmazó lekérdezések 404-as hiba esetén meghiúsulnak.

A szinonimák leképezésének létrehozása, frissítése és törlése mindig egy teljes dokumentumból álló művelet, ami azt jelenti, hogy a szinonimák leképezésének részeit nem lehet Növekményesen frissíteni vagy törölni. Még egy szabály frissítéséhez is szükség van egy újratöltésre.

## <a name="assign-synonyms-to-fields"></a>Szinonimák kiosztása a mezőkhöz

Egy szinonima-Térkép feltöltését követően engedélyezheti a szinonimákat a típus mezőin `Edm.String` vagy a `Collection(Edm.String)` mezőkön `"searchable":true` . Ahogy azt említettük, egy mező definíciója csak egy szinonimát használhat.

```http
POST /indexes?api-version=2020-06-30
{
    "name":"hotels-sample-index",
    "fields":[
        {
            "name":"description",
            "type":"Edm.String",
            "searchable":true,
            "synonymMaps":[
            "en-synonyms"
            ]
        },
        {
            "name":"description_fr",
            "type":"Edm.String",
            "searchable":true,
            "analyzer":"fr.microsoft",
            "synonymMaps":[
            "fr-synonyms"
            ]
        }
    ]
}
```

## <a name="query-on-equivalent-or-mapped-fields"></a>Lekérdezés egyenértékű vagy leképezett mezőkön

A szinonimák hozzáadásával nem kerül sor új követelményekre a lekérdezési konstrukcióban. A kifejezésekkel és kifejezésekkel kapcsolatos lekérdezéseket a szinonimák hozzáadása előtt is kiadhatja. Az egyetlen különbség, hogy ha egy lekérdezési kifejezés szerepel a szinonimák leképezésében, a lekérdezési motor a szabálytól függően kibontja vagy újraírja a kifejezést vagy kifejezést.

## <a name="how-synonyms-are-used-during-query-execution"></a>Szinonimák használata a lekérdezés végrehajtása során

A szinonimák egy lekérdezés-kiterjesztési módszer, amely kiegészíti az indexek tartalmát egyenértékű feltételekkel, de csak olyan mezőknél, amelyek szinonimák hozzárendelésével rendelkeznek. Ha egy mező hatókörű lekérdezése *kizár* egy szinonima-kompatibilis mezőt, akkor nem jelenik meg egyezés a szinonimák leképezésében.

A szinonima-kompatibilis mezők esetében a szinonimákat a társított mezővel megegyező szöveges elemzésnek kell alávetni. Ha például egy mező a standard Lucene Analyzer használatával van elemezve, a szinonimát a lekérdezés időpontjában a standard Lucene Analyzer is feltételként fogja használni. Ha meg szeretné őrizni az írásjeleket, például az időszakokat vagy kötőjeleket, a szinonimák kifejezésében alkalmazzon egy Content-Serving Analyzert a mezőre.

Belsőleg a szinonimák funkció felülírja az eredeti lekérdezést a vagy operátorral való szinonimákkal. Emiatt a kiemelési és pontozási profilok az eredeti kifejezést és a szinonimákat egyenértékűként kezelik.

A szinonimák csak az ingyenes szöveges lekérdezésekre vonatkoznak, és szűrők, dimenziók, automatikus kiegészítések és javaslatok esetén nem támogatottak. Az automatikus kiegészítés és a javaslatok kizárólag az eredeti feltételeken alapulnak; a válaszban nem jelennek meg szinonima egyezések.

A szinonimák bővítései nem érvényesek a helyettesítő karakteres keresési kifejezésekre; az előtag, a fuzzy és a regex kifejezések nincsenek kibontva.

Ha egyetlen lekérdezést kell végrehajtania, amely szinonimákat, valamint helyettesítő karaktereket, regexeket vagy fuzzy kereséseket alkalmaz, a lekérdezéseket a vagy a szintaxis használatával kombinálhatja. Ha például a szinonimákat az egyszerű lekérdezési szintaxishoz helyettesítő karakterekkel kombinálja, a kifejezés a következő lesz: `<query> | <query>*` .

Ha meglévő indexe van egy fejlesztési (nem éles) környezetben, kísérletezzen egy kisméretű szótárral, és nézze meg, hogy a szinonimák hozzáadása hogyan módosítja a keresési élményt, beleértve a pontozási profilok, a kiemelések és a javaslatok hatását.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Szinonima-Térkép létrehozása (REST API)](/rest/api/searchservice/create-synonym-map)
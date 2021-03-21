---
title: Intelligens keresés
titleSuffix: Azure Cognitive Search
description: A "DID it Mean" keresési élményének megvalósításával automatikusan kijavítani a hibásan írt kifejezést vagy az elírást.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: b2f2c8497d5365104a5ffc361b791450925d0c19
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101694784"
---
# <a name="fuzzy-search-to-correct-misspellings-and-typos"></a>A hibás helyesírási hibák és az elírások kijavítása a zavaros kereséssel

Az Azure Cognitive Search támogatja a zavaros keresést, egy olyan típusú lekérdezést, amely kompenzálja az elírásokat és a helytelenül beírt kifejezéseket a bemeneti karakterláncban. Ezt a hasonló kompozíciót tartalmazó kifejezések keresésével végezheti el. A közeli találatok közötti keresés kibontásával automatikusan kijavításra kerül egy elírás, ha az eltérés csupán néhány hibás karakter. 

## <a name="what-is-fuzzy-search"></a>Mi az a zavaros keresés?

Ez egy olyan terjeszkedési gyakorlat, amely egyezést eredményez a hasonló kompozícióval rendelkező feltételekben. Ha meg van adva egy fuzzy keresés, a motor összeállít egy gráfot (amely a [determinisztikus véges automata elmélet](https://en.wikipedia.org/wiki/Deterministic_finite_automaton)alapján) a lekérdezésben szereplő összes kifejezéshez hasonlóan áll. Ha például a lekérdezés három kifejezést tartalmaz: "University of Washington", a lekérdezésben minden kifejezéshez létrejön egy gráf `search=university~ of~ washington~` (nem áll le a leállítási szó a zavaros keresésben, így a "a" diagramot kap).

A gráf akár 50 kiterjesztésből vagy permutációból áll, amelyek mindegyike a folyamat helyes és helytelen változatait rögzíti. A motor ezután visszaadja a válaszban a legmagasabb releváns egyezéseket. 

Az "Egyetem" kifejezéshez hasonlóan a gráf "unversty, Universty, University, Universe, Inverse" lehet. A gráfon megegyező dokumentumok az eredmények között szerepelnek. A szöveget más olyan lekérdezésekkel ellentétben, amelyek ugyanazt a szót ("egerek" és "egér") eltérő formában kezelik, a rendszer az összehasonlításokat a szövegben található nyelvi elemzések nélkül, Face értékként veszi át. A "Universe" és az "Inverse", amely szemantikailag eltérő, az egyeztetést eredményezi, mert a szintaktikai eltérések kicsik.

A egyezés sikeres, ha az eltérések két vagy kevesebb módosításra korlátozódnak, ahol a Szerkesztés beszúrt, törölt, helyettesített vagy átültetett karakter lehet. A különbözetet megadó karakterlánc-korrekciós algoritmus a [Damerau-Levenshtein távolsági](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance) metrika, amely a "minimális számú művelet (a beszúrások, a törlések, a helyettesítések vagy két szomszédos karakter átültetése) szükséges ahhoz, hogy módosítani lehessen egy szót a másikba". 

Az Azure Cognitive Searchban:

+ A fuzzy lekérdezés a teljes kifejezésekre vonatkozik, de a kifejezéseket és az építményeket is támogathatja. Például: "Unviersty ~ of ~" Wshington ~ "megegyeznek a" Washington Egyetem "kifejezéssel.

+ A Szerkesztés alapértelmezett távolsága 2. Egy érték `~0` nem jelent terjeszkedést (csak a pontos kifejezés minősül egyezésnek), de `~1` az egyik eltérést vagy egy módosítást is megadhat. 

+ A zavaros lekérdezések legfeljebb 50 további variációs időszakra bonthatók ki. Ez a korlát nem konfigurálható, de a szerkesztési távolság 1 értékre csökkentése révén hatékonyan csökkentheti a bővítések számát.

+ A válaszok olyan dokumentumokból állnak, amelyek megfelelő egyezést tartalmaznak (legfeljebb 50).

Együttesen a gráfok egyeztetési feltételként lesznek elküldve az indexben lévő jogkivonatokkal szemben. A zavaros keresés a többi lekérdezési űrlapnál is eleve lassabb. Az index mérete és összetettsége határozza meg, hogy az előnyök elegendőek-e a válasz késésének ellensúlyozására.

> [!NOTE]
> Mivel a zavaros keresés lassú, érdemes lehet olyan alternatívákat kivizsgálni, mint például az n-Gram indexelés, a rövid karakteres sorozatok előrehaladásával (két és három karakteres sorozat a bigram és a trigram tokenek esetében). A nyelvtől és a lekérdezési felülettől függően az n-Gram jobb teljesítményt eredményezhet. A kikapcsolás az, hogy az n-Gram indexelés nagyon nagy tárolási igényű, és sokkal nagyobb indexeket hoz létre.
>
> Egy másik alternatíva, amelyet érdemes megfontolnia, hogy csak a legvalószínűbb eseteket szeretné kezelni, [szinonimának](search-synonyms.md)számít-e. Például a "keresés" a "Gyorskeresés, a serch, a sarch" vagy a "lekérés" értékre való leképezéséhez.

## <a name="indexing-for-fuzzy-search"></a>A fuzzy keresés indexelése

A lekérdezés feldolgozásakor a rendszer nem használja az elemzőket a bővítési gráfok létrehozásához, de ez nem jelenti azt, hogy az elemzők figyelmen kívül hagyják a zavaros keresési helyzetekben. Az indexelés során a rendszer az indexelő használatával olyan jogkivonatokat hoz létre, amelyekkel a megfeleltetés megtörténik, függetlenül attól, hogy a lekérdezés szabad formátumú-e, szűrt keresés, vagy egy olyan, a gráftal való intelligens keresés, amely bemenetként szerepel. 

Általában az elemzők mező szerinti kiosztásakor az elemzési lánc finomhangolására vonatkozó döntés az elsődleges használati eset (egy szűrő vagy teljes szöveges keresés) alapján történik, és nem a speciális lekérdezési űrlapokkal, például a fuzzy kereséssel. Ezért nem létezik konkrét elemző javaslat a fuzzy kereséshez. 

Ha azonban a tesztelési lekérdezések nem a várt egyezéseket alkotják, megpróbálkozhat az indexelési elemző megváltoztatásával, ha azt szeretné, hogy [a rendszer jobb](index-add-language-analyzers.md)eredményeket kapjon. Bizonyos nyelvek, különösen a magánhangzó-mutációkkal, kihasználhatják a Microsoft Natural Language processors által generált inflexiós és szabálytalan Word formákat. Bizonyos esetekben a megfelelő nyelvi elemző használatával különbséget tehet abban, hogy a kifejezés jogkivonatos-e a felhasználó által megadott értékkel kompatibilis módon.

## <a name="how-to-use-fuzzy-search"></a>A fuzzy Search használata

A fuzzy lekérdezések a teljes Lucene lekérdezési szintaxis használatával készültek, amely meghívja a [Lucene lekérdezés-elemzőt](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

1. Állítsa be a teljes Lucene-elemzőt a lekérdezésen ( `queryType=full` ).

1. Igény szerint a kérelem hatókörét meghatározott mezőkre is használhatja, ezzel a paraméterrel ( `searchFields=<field1,field2>` ). 

1. Fűzze hozzá a tilde ( `~` ) operátort a teljes időszak végén ( `search=<string>~` ).

   Adjon meg egy 0 és 2 közötti számot (alapértelmezett érték), ha meg szeretné adni a szerkesztési távolságot ( `~1` ). Például a "Blue ~" vagy a "Blue ~ 1" a "Blue", a "blues" és a "ragasztó" értéket fogja visszaadni.

Az Azure Cognitive Search a lejárati idő és a távolság (legfeljebb 2) mellett nem adhat meg további paramétereket a lekérdezéshez.

> [!NOTE]
> A lekérdezések feldolgozásakor a zavaros lekérdezések nem esnek a [lexikális analízisbe](search-lucene-query-architecture.md#stage-2-lexical-analysis). A lekérdezési bemenet közvetlenül a lekérdezési fában lesz hozzáadva, és kibontva a használati feltételek diagramjának létrehozásához. Az egyetlen végrehajtott átalakítás az alsó burkolat.

## <a name="testing-fuzzy-search"></a>Fuzzy Keresés tesztelése

Az egyszerű tesztelés érdekében javasoljuk, hogy a [Search Explorer](search-explorer.md) vagy a [Poster](search-get-started-rest.md) segítségével ismételje meg a lekérdezési kifejezéseket. Mindkét eszköz interaktív, ami azt jelenti, hogy gyorsan elvégezheti egy kifejezés több változatának lépéseit, és kiértékelheti a visszaadott válaszokat.

Ha az eredmények nem egyértelműek, a [találatok kiemelése](search-pagination-page-layout.md#hit-highlighting) segít azonosítani a válaszban szereplő egyezést. 

> [!Note]
> A találatok kiemelésének használata a zavaros egyezések azonosítására korlátozott, és csak az alapszintű fuzzy keresésre használható. Ha az indexben pontozási profilok szerepelnek, vagy ha a lekérdezést további szintaxissal rétegbe emeli, a találatok kiemelése nem tudja azonosítani a egyezést. 

### <a name="example-1-fuzzy-search-with-the-exact-term"></a>1. példa: fuzzy keresés a pontos kifejezéssel

Tegyük fel, hogy a következő sztring létezik egy `"Description"` keresési dokumentum egy mezőjében: `"Test queries with special characters, plus strings for MSFT, SQL and Java."`

Kezdje egy fuzzy kereséssel a "speciális" kifejezésre, és vegyen fel találatot kiemelve a Leírás mezőbe:

```console
search=special~&highlight=Description
```

A válaszban, mivel a találatok kiemelését adta hozzá, a formázás a "speciális" értékre lesz alkalmazva a megfelelő kifejezésként.

```output
"@search.highlights": {
    "Description": [
        "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
    ]
```

Próbálja megismételni a kérést, és a "speciális" kifejezés elindításával több betűt ("PE") kell kiírnia:

```console
search=scial~&highlight=Description
```

Eddig nem változik a válasz. Az alapértelmezett 2 fokos távolságot használva a "speciális" két karakterből álló "PE" eltávolítása továbbra is lehetővé teszi az adott időszak sikeres egyeztetését.

```output
"@search.highlights": {
    "Description": [
        "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
    ]
```

Még egy kérelem kipróbálása, a keresési kifejezés további módosítása: egy utolsó karakter megadásával összesen három törlésre (a "speciális" értékről a "skálázásra"):

```console
search=scal~&highlight=Description
```

Figyelje meg, hogy ugyanazt a választ adja vissza, de mostantól nem a "speciális" értékre, hanem az "SQL" kifejezésre.

```output
        "@search.score": 0.4232868,
        "@search.highlights": {
            "Description": [
                "Mix of special characters, plus strings for MSFT, <em>SQL</em>, 2019, Linux, Java."
            ]
```

Ennek a kibővített példának a lényege, hogy illusztrálja, hogy a találatok kiemelése nem egyértelmű eredményeket hoz. A rendszer minden esetben ugyanazt a dokumentumot adja vissza. Ha a dokumentumok azonosítóit használta a egyezés ellenőrzéséhez, lehetséges, hogy kihagyta a "speciális" és "SQL" közötti váltást.

## <a name="see-also"></a>Lásd még

+ [A teljes szöveges keresés működése az Azure Cognitive Searchban (lekérdezési elemzési architektúra)](search-lucene-query-architecture.md)
+ [Keresési ablak](search-explorer.md)
+ [Lekérdezés a .NET-ben](./search-get-started-dotnet.md)
+ [A lekérdezés a REST-ben](./search-get-started-powershell.md)
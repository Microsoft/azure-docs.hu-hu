---
title: Nyelvi elemzők hozzáadása karakterlánc-mezőkhöz
titleSuffix: Azure Cognitive Search
description: Többnyelvű lexikális analízis a nem angol nyelvű lekérdezésekhez és indexekhez az Azure Cognitive Searchban.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/05/2020
ms.openlocfilehash: 8f0909ee1cdce1e6180b91a30b2e9b281098c826
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85130551"
---
# <a name="add-language-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Nyelvi elemzők hozzáadása karakterlánc-mezőkhöz Azure Cognitive Search indexben

A *Language Analyzer* egy adott típusú [szöveges elemző](search-analyzers.md) , amely lexikális analízist hajt végre a célnyelv nyelvi szabályainak használatával. Minden kereshető mező rendelkezik egy **Analyzer** tulajdonsággal. Ha a tartalma lefordított karakterláncokból áll, például az angol és a Kínai szöveg különálló mezőiből, megadhatja az egyes mezők nyelvi elemzőit az elemzők gazdag nyelvi képességeinek eléréséhez.

## <a name="when-to-use-a-language-analyzer"></a>Mikor kell nyelvi elemzőt használni

Ha a Word vagy a mondat struktúrájának ismerete, a szöveg elemzésekor figyelembe kell vennie egy nyelvi elemzőt. Gyakori példa a szabálytalan műveleti űrlapok ("Bring" és "benyújtott"), vagy a többes számú főnevek ("egerek" és "egér") társítása. Nyelvi tájékoztatás nélkül ezeket a karakterláncokat csak a fizikai jellemzőkre elemezzük, ami nem fogja tudni megfogni a kapcsolódást. Mivel a nagy méretű adathalmazok nagyobb valószínűséggel rendelkeznek a tartalommal, a leírásokból, felülvizsgálatokból és összegzésből álló mezők jó választást jelentenek a nyelvi elemzők számára.

Érdemes figyelembe venni a nyelvi elemzőket is, ha a tartalom nem nyugati nyelvű karakterláncokból áll. Habár az [alapértelmezett analizátor](search-analyzers.md#default-analyzer) nyelvtől független, a szóközök és speciális karakterek (kötőjelek és perjelek) használata a karakterláncok elkülönítésére általában a nyugati nyelvekre vonatkozik, mint a nem nyugatiak. 

Például kínai, Japán, Koreai (CJK) és más ázsiai nyelveken a szóköz nem feltétlenül a szó elválasztója. Vegye figyelembe a következő japán karakterláncot. Mivel nem tartalmaz szóközöket, egy nyelvtől független elemző valószínűleg egyetlen tokenként elemezni fogja a teljes karakterláncot, ha valójában a karakterlánc valójában egy kifejezés.

```
これは私たちの銀河系の中ではもっとも重く明るいクラスの球状星団です。
(This is the heaviest and brightest group of spherical stars in our galaxy.)
```

A fenti példában egy sikeres lekérdezésnek tartalmaznia kell a teljes tokent, vagy egy részleges tokent, amely utótag helyettesítő karaktert használ, ami nem természetes és nem korlátozó keresési élményt eredményez.

Jobb megoldás, ha egyéni szavakat keres: 明るい (Bright), 私たちの (mi), 銀河系 (Galaxy). A Cognitive Searchban elérhető japán elemzők egyikének használata nagyobb valószínűséggel oldja meg ezt a viselkedést, mivel ezek az elemzők jobban illeszkednek a szöveg részleteit leíró szavakba a célként megadott nyelven.

## <a name="comparing-lucene-and-microsoft-analyzers"></a>A Lucene és a Microsoft-elemzők összehasonlítása

Az Azure Cognitive Search 35 támogatja a Lucene által támogatott nyelvi elemzőket, valamint az Office és a Bing által használt, a Microsoft természetes nyelvi feldolgozási technológiája által támogatott 50 nyelvi elemzőket.

Előfordulhat, hogy egyes fejlesztők inkább a Lucene ismerős, egyszerű és nyílt forráskódú megoldását részesítik előnyben. A Lucene nyelvi elemzői gyorsabbak, de a Microsoft-elemzők fejlett funkciókkal rendelkeznek, például a morfológiai elemzéshez, a szavak összetételét (például a német, a dán, a holland, a svéd, a norvég, az észt, a Finish, a magyar és a szlovák nyelv) és az entitások felismerését (URL-címek, e-mailek, dátumok Ha lehetséges, a Microsoft és a Lucene elemzők összehasonlítását is el kell végeznie, hogy eldöntse, melyik egy jobb illeszkedés. 

A Microsoft-elemzők indexelése a nyelvtől függően a Lucene egyenértékűként átlagosan két-háromszor lassabban történik. A keresési teljesítmény nem lehet jelentős hatással az átlagos méretű lekérdezéseknél. 

### <a name="english-analyzers"></a>Angol analizátorok

Az alapértelmezett elemző a standard Lucene, amely az angol nyelvhez jól működik, de talán nem, valamint a Lucene angol analizátor vagy a Microsoft angol analizátora. 
 
+ A Lucene angol analizátora kiterjeszti a standard Analyzert. Eltávolítja a birtokosok (záró) szavakat a szavakból, és a porton kívüli algoritmust alkalmazza, és eltávolítja az angol leállítási szavakat.  

+ A Microsoft angol Analyzer morfológiai elemzéshez hajt végre a művelet helyett. Ez azt jelenti, hogy az ragozott és a szabálytalan Word-űrlapokat sokkal jobban kezelheti, ami több releváns keresési eredményt eredményez 

## <a name="configuring-analyzers"></a>Elemzők konfigurálása

A nyelvi elemzők a következőképpen használhatók:. Az index definíciójának minden mezőjénél megadhatja az **analizátor** tulajdonságot egy Analyzer-névre, amely megadja a nyelv és a nyelvi készlet (Microsoft vagy Lucene) értékét. Ugyanez az analizátor lesz alkalmazva a mező indexeléséhez és kereséséhez. Megadhatja például, hogy az angol, a francia és a spanyol nyelvű szállodai leírások külön mezői legyenek, amelyek ugyanabban az indexben vannak egymás mellett.

> [!NOTE]
> Egy adott mezőnél nem lehet más nyelvi elemzőt használni, mint a lekérdezés időpontjában. Ez a képesség [Egyéni elemzők](index-add-custom-analyzers.md)számára van fenntartva. Ezért ha a **searchAnalyzer** vagy a **indexAnalyzer** tulajdonságot a Language Analyzer nevére próbálja beállítani, a REST API hibaüzenetet ad vissza. Ehelyett a **Analyzer** tulajdonságot kell használnia.

A **searchFields** lekérdezési paraméterrel megadásával meghatározhatja, hogy melyik nyelvspecifikus mezőt szeretné keresni a lekérdezésekben. Áttekintheti azokat a lekérdezési példákat is, amelyek tartalmazzák a Analyzer tulajdonságot a [keresési dokumentumokban](https://docs.microsoft.com/rest/api/searchservice/search-documents). 

További információ az index tulajdonságairól: [index létrehozása &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index). Az Azure Cognitive Search elemzésével kapcsolatos további információkért lásd: [elemzők az Azure-ban Cognitive Search](https://docs.microsoft.com/azure/search/search-analyzers).

<a name="language-analyzer-list"></a>

## <a name="language-analyzer-list"></a>Nyelvi analizátorok listája 
 Alább látható a támogatott nyelvek listája, valamint a Lucene és a Microsoft Analyzer neve.  

|Nyelv|Microsoft Analyzer neve|Lucene Analyzer neve|  
|--------------|-----------------------------|--------------------------|  
|Arab|ar. Microsoft|ar. Lucene|  
|örmény||Lucene|  
|Bangla|bn. Microsoft||  
|Baszk||EU. Lucene|  
|Bolgár|BG. Microsoft|BG. Lucene|  
|Katalán|CA. Microsoft|CA. Lucene|  
|kínai (egyszerűsített)|zh-Hans. Microsoft|zh-Hans. Lucene|  
|kínai (hagyományos)|zh-Hant. Microsoft|zh-Hant. Lucene|  
|Horvát|HR. Microsoft||  
|Cseh|cs. Microsoft|cs. Lucene|  
|Dán|da. Microsoft|da. Lucene|  
|Holland|nl. Microsoft|nl. Lucene|  
|Angol|en. Microsoft|en. Lucene|  
|Észt|et. Microsoft||  
|Finn|Fi. Microsoft|Fi. Lucene|  
|Francia|fr. Microsoft|fr. Lucene|  
|Gallego||Gl. Lucene|  
|Német|de. Microsoft|de. Lucene|  
|Görög|el. Microsoft|el. Lucene|  
|gudzsaráti|Gu. Microsoft||  
|Héber|ő. Microsoft||  
|Hindi|Hi. Microsoft|Hi. Lucene|  
|Magyar|hu. Microsoft|hu. Lucene|  
|Izlandi|a. Microsoft||  
|Indonéz (Bahasa)|azonosító. Microsoft|ID. Lucene|  
|Ír||ga. Lucene|  
|Olasz|it. Microsoft|it. Lucene|  
|Japán|ja. Microsoft|ja. Lucene|  
|kannada|KN. Microsoft||  
|Koreai|ko. Microsoft|ko. Lucene|  
|Lett|lv. Microsoft|lv. Lucene|  
|Litván|lt. Microsoft||  
|malajálam|ml. Microsoft||  
|Maláj (latin betűs)|MS. Microsoft||  
|marathi|Mr. Microsoft||  
|Norvég|NB. Microsoft|nem. Lucene|  
|perzsa||fa. Lucene|  
|Lengyel|pl. Microsoft|pl. Lucene|  
|Portugál (Brazília)|pt-br. Microsoft|pt-br. Lucene|  
|Portugál (Portugália)|PT-pt. Microsoft|PT-pt. Lucene|  
|pandzsábi|PA. Microsoft||  
|Román|ro. Microsoft|ro. Lucene|  
|Orosz|ru. Microsoft|ru. Lucene|  
|Szerb (cirill betűs)|SR-cirill betűs. Microsoft||  
|Szerb (latin betűs)|SR-latin. Microsoft||  
|Szlovák|sk. Microsoft||  
|Szlovén|SL. Microsoft||  
|Spanyol|es. Microsoft|es. Lucene|  
|Svéd|Sv. Microsoft|Sv. Lucene|  
|tamil|ta. Microsoft||  
|telugu|te. Microsoft||  
|Thai|th. Microsoft|th. Lucene|  
|Török|TR. Microsoft|TR. Lucene|  
|Ukrán|Egyesült Királyság. Microsoft||  
|urdu|a. Microsoft||  
|Vietnámi|VI. Microsoft||  

 Az [Apache Lucene nyelvi elemzői](https://lucene.apache.org/core/6_6_1/core/overview-summary.html )a **Lucene** -mel ellátott nevekkel rendelkező elemzőket használják.

## <a name="see-also"></a>Lásd még  

+ [Index létrehozása &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  

+ [AnalyzerName osztály](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername)  


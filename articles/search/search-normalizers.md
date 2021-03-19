---
title: Szűrők, aspektusok, rendezés szövegének normalizálása
titleSuffix: Azure Cognitive Search
description: Megadhatja az indexben lévő szöveges mezőkhöz tartozó normalizáló mezőket, így testre szabhatja a szűréshez, az elemzéshez és a rendezéshez szükséges szigorú kulcsszavas viselkedést
author: IshanSrivastava
manager: jlembicz
ms.author: ishansri
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/23/2021
ms.openlocfilehash: 3e7a33d9213d7af44d2cfc50baa847534618f7e5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609218"
---
# <a name="text-normalization-for-case-insensitive-filtering-faceting-and-sorting"></a>Szöveg normalizálása kis-és nagybetűk megkülönböztetése esetén – szűrés, aspektus és rendezés

 > [!IMPORTANT]
 > A normalizáló nyilvános előzetes verzióban érhető el, amely a **2020-06-30-es előzetes verziójú REST API** elérhető. Az előzetes verziójú funkciók a kiegészítő használati feltételek mellett is elérhetők.

A dokumentumok Azure Cognitive Search indexből való kereséséhez és lekéréséhez a lekérdezést a dokumentum tartalmához kell egyeztetni. A tartalom elemezhető úgy, hogy jogkivonatokat hozzon létre a megfeleltetéshez, mint a paraméter használata esetén `search` , vagy használható a-ként a szigorú kulcsszavas egyeztetéshez, mint a `$filter` , a és a `facets` `$orderby` . Ez a teljes körű megközelítés a legtöbb szituációra vonatkozik, de rövid idő alatt az egyszerű, például a burkolat, a hangsúly eltávolítása, a asciifolding és így továbbra is szükség van a teljes elemzési láncon keresztüli végrehajtásra.

Tekintse meg a következő példákat:

+ `$filter=City eq 'Las Vegas'` a csak azokat a dokumentumokat fogja visszaadni, amelyek tartalmazzák a "Las Vegas" szöveget, és a "Las Vegas" és a "Las Vegas" szöveggel kizárják a dokumentumokat, ami nem megfelelő, ha a használati esettől függetlenül minden dokumentumra szükség van.

+ `search=*&facet=City,count:5` a "Las Vegas", "LAS VEGAS" és "Las Vegas" értéket ad vissza külön értékként, annak ellenére, hogy ugyanaz a város.

+ `search=usa&$orderby=City` a a lexicographical-sorrendbe tartozó városokat fogja visszaadni: "Las Vegas", "Seattle", "Las Vegas", még akkor is, ha az a cél, hogy az adott esettől függetlenül azonos városokat rendeljen egymáshoz. 

## <a name="normalizers"></a>Normalizálók

A *normalizáló* az a keresőmotornak a kulcsszavas egyeztetéshez felelős összetevője. A normalizálók hasonlók az elemzők számára, kivéve, ha nem tokenize a lekérdezést. A normalizálók használatával megvalósítható átalakítások némelyike a következőkre használható:

+ Kis-és nagybetűvé alakítja.
+ Az "o" és az "e" ASCII-karaktereknek megfelelő ékezeteket és mellékjeleket, például √ vagy ê kell normalizálni.
+ Leképezheti `-` a karaktereket és a szóközöket egy felhasználó által megadott karakterre.

A normalizálók megadhatók az index szöveges mezőiben, és az indexelés és a lekérdezés végrehajtásakor is alkalmazva lesznek.

## <a name="predefined-and-custom-normalizers"></a>Előre definiált és egyéni normalizálók 

Az Azure Cognitive Search támogatja az előre definiált normalizálók használatát a gyakori használati esetekhez, valamint a szükséges testreszabási képességeket.

| Kategória | Leírás |
|----------|-------------|
| [Előre definiált normalizálók](#predefined-normalizers) | A beépített és a konfiguráció nélkül is használható. |
|[Egyéni normalizálók](#add-custom-normalizers) | Speciális forgatókönyvek esetén. A már meglévő elemek kombinációjának felhasználó által definiált konfigurációját igényli, amely a karakteres és a jogkivonat-szűrőkből áll. <sup>1</sup>|

<sup>(1)</sup> az egyéni normalizálók nem határoznak meg tokenizers, mert a normalizálók mindig egyetlen tokent hoznak létre.

## <a name="how-to-specify-normalizers"></a>Normalizálók megadásának módja

A normalizálók a szövegmezőben ( `Edm.String` és) a ( `Collection(Edm.String)` z), `filterable` `sortable` , vagy `facetable` tulajdonság értéke True értékre van állítva. A normalizáló beállítása nem kötelező, és alapértelmezés szerint ez a beállítás `null` . Javasoljuk, hogy az előre definiált normalizálók kiértékelése előtt konfigurálja az egyéni konfigurációt az egyszerű használat érdekében. Ha az eredmények nem vártak, próbálkozzon más normalizáló értékkel.

A normalizálók csak akkor adhatók meg, ha új mező kerül be az indexbe. Javasoljuk, hogy vizsgálja meg a normalizálás szükségességét, és rendeljen normalizált a fejlesztés kezdeti szakaszaiban az indexek eldobásakor és újbóli létrehozásakor. A normalizálók nem adhatók meg olyan mezőn, amely már létre lett hozva.

1. Ha a mező definícióját az [indexben](/rest/api/searchservice/create-index)hozza létre, állítsa a  **normalizáló** tulajdonságot a következők egyikére: egy [előre definiált normalizáló](#predefined-normalizers) , például `lowercase` vagy egy egyéni normalizáló (ugyanabban az index sémában van megadva).  
 
   ```json
   "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "filterable": true,
      "analyzer": "en.microsoft",
      "normalizer": "lowercase"
      ...
    },
   ```

2. Először az index **[normalizáló]** szakaszában kell definiálni az egyéni normalizálók értékét, majd hozzá kell rendelni a mező definícióját az előző lépésben látható módon. További információ: [index létrehozása](/rest/api/searchservice/create-index) és [Egyéni normalizálók hozzáadása](#add-custom-normalizers).


   ```json
   "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": null,
      "normalizer": "my_custom_normalizer"
    },
   ```

 
> [!NOTE]
> Egy meglévő mező normalizáló módosításához újra kell építenie az indexet (nem lehet újraépíteni az egyes mezőket).

Az éles indexekhez megfelelő megkerülő megoldás, ahol az indexek újraépítése költséges, egy új mező létrehozása, amely megegyezik a régivel, de az új normalizáló is, és a régi helyett használja. A [frissítési index](/rest/api/searchservice/update-index) használatával vegye fel az új mezőt és a [mergeOrUpload](/rest/api/searchservice/addupdate-or-delete-documents) a feltöltéshez. Később, a tervezett indexek karbantartásának részeként törölheti az indexet az elavult mezők eltávolításához.

## <a name="add-custom-normalizers"></a>Egyéni normalizálók hozzáadása

Az egyéni normalizálók definiálva vannak az index sémában, és a Field tulajdonsággal adhatók meg. Az egyéni normalizáló definíciója tartalmaz egy nevet, egy típust, egy vagy több char szűrőt és egy jogkivonat-szűrőt. A char szűrők és a jogkivonat-szűrők az egyéni normalizáló építőelemei, és a szöveg feldolgozásához felelősek. Ezek a szűrők balról jobbra vannak alkalmazva.

 A a `token_filter_name_1` jogkivonat-szűrő neve, és `char_filter_name_1` `char_filter_name_2` a karakteres szűrők nevei (lásd a [támogatott jogkivonat-szűrők](#supported-token-filters) és a char Filters alábbi táblázatait az érvényes értékekhez).

A normalizáló definíciója a nagyobb index részét képezi. Az index további részével kapcsolatos információkért lásd: [create index API](/rest/api/searchservice/create-index) .

```
"normalizers":(optional)[
   {
      "name":"name of normalizer",
      "@odata.type":"#Microsoft.Azure.Search.CustomNormalizer",
      "charFilters":[
         "char_filter_name_1",
         "char_filter_name_2"
      ],
      "tokenFilters":[
         "token_filter_name_1
      ]
   }
],
"charFilters":(optional)[
   {
      "name":"char_filter_name_1",
      "@odata.type":"#char_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenFilters":(optional)[
   {
      "name":"token_filter_name_1",
      "@odata.type":"#token_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
]
```

Az index létrehozásakor vagy később is hozzáadhatók egyéni normalizálók egy meglévő frissítéssel. Ha egyéni normalizáló ad hozzá egy meglévő indexhez, a **allowIndexDowntime** jelzőt meg kell adni a [frissítési indexben](/rest/api/searchservice/update-index) , és az index néhány másodpercig nem lesz elérhető.

## <a name="normalizers-reference"></a>Normalizálók referenciája

### <a name="predefined-normalizers"></a>Előre definiált normalizálók
|**Név**|**Leírás és beállítások**|  
|-|-|  
|Standard| A szöveg kisbetűt, majd a asciifolding.|  
|kisbetűket| A karaktereket kisbetűsre alakítja.|
|nagybetűk| A karaktereket nagybetűssé alakítja.|
|asciifolding| A nem az alapszintű latin Unicode blokkban lévő karaktereket az ASCII-egyenértékű értékre alakítja, ha van ilyen. Például a következőre változik: à.|
|elision| Eltávolítja a elision a jogkivonatok elejéről.|

### <a name="supported-char-filters"></a>Támogatott char-szűrők
A char szűrőkről további részleteket a [char Filters dokumentációjában](index-add-custom-analyzers.md#CharFilter)találhat.
+ [leképezés](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/MappingCharFilter.html)  
+ [pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceCharFilter.html)

### <a name="supported-token-filters"></a>Támogatott jogkivonat-szűrők
Az alábbi lista a normalizálók által támogatott jogkivonat-szűrőket mutatja be, és a lexikális analízisben részt vevő teljes jogkivonat-szűrők részhalmaza. A szűrőkkel kapcsolatos további részletekért tekintse meg a [jogkivonat-szűrők ismertetése](index-add-custom-analyzers.md#TokenFilters)című témakört.

+ [arabic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)
+ [asciifolding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)
+ [cjk_width](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)  
+ [elision](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)  
+ [german_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)
+ [hindi_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)  
+ [indic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)
+ [persian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)
+ [scandinavian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)  
+ [scandinavian_folding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)
+ [sorani_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)  
+ [kisbetűket](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)
+ [nagybetűk](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)


## <a name="custom-normalizer-example"></a>Egyéni normalizáló példa

Az alábbi példa egy egyéni normalizáló definíciót mutat be a megfelelő char Filters és token Filters használatával. A char szűrők és a jogkivonat-szűrők egyéni beállításai külön vannak megadva, mint nevesített szerkezetek, majd a normalizáló definíciójában hivatkoznak rá az alábbi ábrán látható módon.

* A "my_custom_normalizer" egyéni normalizáló az `normalizers` index definíciójának szakaszában van definiálva.
* A normalizáló két karakterből álló szűrőből és három jogkivonat-szűrőből áll: elision, kisbetűs és testreszabott asciifolding szűrő "my_asciifolding".
* A "map_dash" első karakteres szűrője az aláhúzással lecseréli az összes kötőjelet, míg a második "remove_whitespace" eltávolítja az összes szóközt.

```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false,
        },
        {
           "name":"city",
           "type":"Edm.String",
           "filterable": true,
           "facetable": true,
           "normalizer": "my_custom_normalizer"
        }
     ],
     "normalizers":[
        {
           "name":"my_custom_normalizer",
           "@odata.type":"#Microsoft.Azure.Search.CustomNormalizer",
           "charFilters":[
              "map_dash",
              "remove_whitespace"
           ],,
           "tokenFilters":[              
              "my_asciifolding",
              "elision",
              "lowercase",
           ]
        }
     ],
     "charFilters":[
        {
           "name":"map_dash",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["-=>_"]
        },
        {
           "name":"remove_whitespace",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["\\u0020=>"]
        }
     ],
     "tokenFilters":[
        {
           "name":"my_asciifolding",
           "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
           "preserveOriginal":true
        }
     ]
  }
```

## <a name="see-also"></a>Lásd még
+ [Nyelvi és szövegszerkesztés-elemzők](search-analyzers.md)

+ [Dokumentumok keresése – REST API](/rest/api/searchservice/search-documents) 

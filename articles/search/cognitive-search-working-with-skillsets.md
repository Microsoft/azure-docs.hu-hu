---
title: Készségkészlet-fogalmak és-munkafolyamatok
titleSuffix: Azure Cognitive Search
description: A szakértelmével egy mesterséges intelligenciát létrehozó folyamat készítése az Azure Cognitive Searchban. Ismerje meg a készségkészlet-összeállítással kapcsolatos fontos fogalmakat és részleteket.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: b5a893ee1923ba4b2bec53b20fb164337bd65902
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96558113"
---
# <a name="skillset-concepts-in-azure-cognitive-search"></a>Az Azure Cognitive Search készségkészlet kapcsolatos fogalmak

Ez a cikk azoknak a fejlesztőknek szól, akiknek alaposabban meg kell ismerniük a készségkészlet fogalmakat és az összetételt, és ismerniük kell a mesterséges intelligenciával kapcsolatos folyamatokat. Ha még nem ismeri ezt a fogalmat, kezdje az [AI-bővítést az Azure Cognitive Searchban](cognitive-search-concept-intro.md).

## <a name="introducing-skillsets"></a>A szakértelmével bemutatása

A készségkészlet az Azure Cognitive Search újrafelhasználható erőforrása, amely egy indexelő szolgáltatáshoz van csatolva, és meghatározza a szöveges vagy képi tartalmak indexelés közbeni elemzéséhez, átalakításához és bővítéséhez használt szaktudás gyűjteményét. A képzettségek bemenetekkel és kimenetekkel rendelkeznek, és gyakran egy adott képesség kimenete lesz egy másik láncban vagy folyamatok sorozatából.

A készségkészlet három fő tulajdonsága van:

+ `skills`a szaktudás rendezetlen gyűjteménye, amelyhez a platform meghatározza a végrehajtás sorrendjét az egyes képességekhez szükséges bemenetek alapján.
+ `cognitiveServices`egy olyan Cognitive Services erőforrás kulcsa, amely a beépített képességeket tartalmazó szakértelmével képeket és szöveges feldolgozást végez.
+ `knowledgeStore`(nem kötelező) egy Azure Storage-fiók, amelyben a dúsított dokumentumok lesznek kitervezve. A bővített dokumentumokat a keresési indexek is használják.

A szakértelmével JSON-ban vannak létrehozva. A következő példa a [Hotel-Reviews készségkészlet](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotelreviews/HotelReviews_skillset.json)egy kis mértékben egyszerűsített verziója, amely a jelen cikkben szereplő fogalmak szemléltetésére szolgál. 

Az első két képesség alább látható:

+ A skill #1 egy olyan [szöveges felosztási képesség](cognitive-search-skill-textsplit.md) , amely a "reviews_text" mező tartalmát bemenetként fogadja el, és a tartalmat a 5000 karakterből álló "Pages" értékre osztja kimenetként.
+ A skill #2 egy [érzelem-észlelési képesség](cognitive-search-skill-sentiment.md) , amely a "lapok" bemenetként fogadja el, és egy új, "hangulat" nevű mezőt hoz létre, amely az érzelmek elemzésének eredményét tartalmazza.


```json
{
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "name": "#1",
            "description": null,
            "context": "/document/reviews_text",
            "defaultLanguageCode": "en",
            "textSplitMode": "pages",
            "maximumPageLength": 5000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/reviews_text"
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
            "name": "#2",
            "description": null,
            "context": "/document/reviews_text/pages/*",
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/reviews_text/pages/*",
                }
            ],
            "outputs": [
                {
                    "name": "score",
                    "targetName": "Sentiment"
                }
            ]
        },
  "cognitiveServices": null,
  "knowledgeStore": {  }
}
```
> [!NOTE]
> A kifejezések létrehozásához a [feltételes képesség](cognitive-search-skill-conditional.md) használatával összetett szakértelmével hozhat létre a hurok és az elágazás segítségével. A szintaxis a [JSON-mutató](https://tools.ietf.org/html/rfc6901) útvonalának jelölésén alapul, és néhány módosítással azonosítja a csomópontokat a dúsítási fában. A egy `"/"` szinttel lejjebb halad a fában, és a  `"*"` -minden operátorként működik a környezetben. Ebben a cikkben számos példa szemlélteti a szintaxist. 

### <a name="enrichment-tree"></a>Dúsítási fa

A [dúsítási folyamat lépéseinek](cognitive-search-concept-intro.md#enrichment-steps)végrehajtása során a tartalom feldolgozása a *dokumentum repedési* fázisát követi, ahol a szöveg és a képek kinyerve lettek a forrásból. A képtartalom ezután átirányítható a képfeldolgozást megadó készségekre, míg a szöveges tartalom a szöveges feldolgozáshoz várólistára kerül. A nagy mennyiségű szöveget tartalmazó forrásbizonylatok esetében beállítható egy *elemzési mód* az indexelő eszközön a szöveg kisebb adattömbökbe való szegmentálásához az optimális feldolgozás érdekében. 

![Knowledge Store a folyamat ábráján](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "Knowledge Store a folyamat ábráján")

Ha egy dokumentum a dúsítási folyamatban van, akkor a rendszer a tartalom és a hozzájuk kapcsolódó dúsítások fájáként jeleníti meg. Ezt a fát a dokumentum repedésének kimenete hozza létre.  A dúsítási fa formátuma lehetővé teszi, hogy a dúsítási folyamat a metaadatokat még a primitív adattípusokhoz csatolja, nem egy érvényes JSON-objektum, de érvényes JSON formátumba is beilleszthető. A következő táblázat a dúsítási folyamatba bekerülő dokumentum állapotát mutatja be:

|AdatSource\Parsing mód|Alapértelmezett|JSON, JSON-sorok & CSV|
|---|---|---|
|Blob Storage|/document/content<br>/Document/normalized_images/*<br>…|/document/{key1}<br>/document/{key2}<br>…|
|SQL|/document/{column1}<br>/document/{column2}<br>…|N/A |
|Cosmos DB|/document/{key1}<br>/document/{key2}<br>…|N/A|

 A képességek végrehajtásával új csomópontokat vesznek fel a dúsítási fában. Ezek az új csomópontok ezután az alárendelt képességekhez bemenetként, a Knowledge Store-ban való kivetítéssel, vagy az index mezőihez való leképezéssel használhatók. A dúsítások nem változtathatók meg, a csomópontok nem szerkeszthetők. Mivel a szakértelmével összetettebbek, így a dúsítási fában marad, de a dúsítási fában nem minden csomópontnak kell azt az indexbe vagy a tudásbázisba tenni. 

A dúsítások csak egy részhalmazát különítheti el az indexbe vagy a Knowledge Store-ba.

### <a name="context"></a>Környezet

Minden egyes szaktudáshoz környezet szükséges. A környezet meghatározza A következőket:

+ A képzettség végrehajtásának száma a kiválasztott csomópontok alapján. A gyűjtemény típusú környezeti értékek esetében a végén a Hozzáadás `/*` után a rendszer egyszer meghívja a képességet a gyűjtemény minden példánya számára. 

+ Ahol a dúsítási fában a rendszer felveszi a képzettségi kimeneteket. A rendszer mindig hozzáadja a kimeneteket a fában a környezeti csomópont gyermekeiként. 

+ A bemenetek alakja. Többszintű gyűjtemények esetén a szülő-gyűjtemény kontextusának beállítása hatással lesz a szakértelem bemenetének alakzatára. Ha például az országok/régiók listáját tartalmazó dúsítási fában van egy lista, a rendszer minden olyan állapotot tartalmaz, amely tartalmazza a ZIP-kódok listáját.

|Környezet|Bevitel|Bemenet alakja|Szaktudás meghívása|
|-------|-----|--------------|----------------|
|`/document/countries/*` |`/document/countries/*/states/*/zipcodes/*` |Az országban/régióban lévő összes ZIP-kód listája |Országonként/régiónként |
|`/document/countries/*/states/*` |"/Document/countries/*/States/*/ZipCodes/*" |Az állapotban lévő ZIP-kódok listája | Az ország/régió és az állapot kombinációja után|

## <a name="generate-enriched-data"></a>Dúsított adathalmazok előállítása 

A [Hotel Reviews készségkészlet](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotelreviews/HotelReviews_skillset.json) használata hivatkozási pontként a következőt tekintjük át:

+ A dúsítási fa fejlődése az egyes képességek végrehajtásával
+ A környezet és a bemenetek működése annak megállapítása érdekében, hogy a szaktudás hányszor fut
+ A bemenet alakja a kontextuson alapul.

A dúsítási folyamaton belüli "dokumentum" a hotel_reviews.csv forrásfájl egyetlen sorát jelöli (a szállodai áttekintést).

### <a name="skill-1-split-skill"></a>Skill #1: megosztási képesség

Ha a forrás tartalma nagy mennyiségű szöveget tartalmaz, hasznos lehet kisebb összetevőkre bontani a nyelv, a hangulat és a kulcsfontosságú kifejezések észlelésének nagyobb pontossága érdekében. Két szem érhető el: lapok és mondatok. Egy oldal körülbelül 5000 karakterből áll.

A szöveges felosztási képességek általában először egy készségkészlet.

```json
      "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
      "name": "#1",
      "description": null,
      "context": "/document/reviews_text",
      "defaultLanguageCode": "en",
      "textSplitMode": "pages",
      "maximumPageLength": 5000,
      "inputs": [
        {
          "name": "text",
          "source": "/document/reviews_text"
        }
      ],
      "outputs": [
        {
          "name": "textItems",
          "targetName": "pages"
        }
```

A a képességek kontextusában `"/document/reviews_text"` a megosztási készség egyszer fog megjelenni `reviews_text` . A szakértelem kimenete egy lista, amelyben a `reviews_text` rendszer 5000 karakterből álló szegmensbe kerül. A felosztott képességből származó kimenet neve `pages` , és hozzá lesz adva a dúsítási fában. A `targetName` funkció lehetővé teszi a képzettségi kimenet átnevezését a dúsítási fában való felvétel előtt.

A dúsítási fában már van egy új csomópont, amely a szakértelem kontextusában van elhelyezve. Ez a csomópont bármilyen képzettségi, leképezési vagy kimeneti mezők leképezéséhez elérhető. Elméletileg a fa a következőképpen néz ki:

![a dúsítás fája a dokumentum repedése után](media/cognitive-search-working-with-skillsets/enrichment-tree-doc-cracking.png "A dúsítási fa a dokumentum repedése és a szaktudás végrehajtása előtt")

Az összes dúsítás legfelső szintű csomópontja `"/document"` . A blob-indexelő használatakor a `"/document"` csomópont a és a alárendelt csomópontjaival fog rendelkezni `"/document/content"` `"/document/normalized_images"` . A CSV-adatkapcsolatok használatakor, ahogy ebben a példában vagyunk, az oszlopnevek az alatta lévő csomópontokra lesznek leképezve `"/document"` . 

A csomópont által egy adott képességhez hozzáadott alkoholtartalom-növeléshez a dúsítás teljes elérési útjára van szükség. Ha például a csomópont szövegét bemenetként kívánja használni ```pages``` egy másik képességhez, meg kell adnia a következőt: ```"/document/reviews_text/pages/*"``` .
 
 ![a bővítési fa a szaktudás után #1](media/cognitive-search-working-with-skillsets/enrichment-tree-skill1.png "A szaktudás #1 végrehajtása után a dúsítási fa")

### <a name="skill-2-language-detection"></a>A szaktudás #2 a nyelvfelismerés

A szállodai felülvizsgálati dokumentumok közé tartozik az ügyfelek visszajelzése több nyelven. A nyelvi észlelési képesség meghatározza, hogy melyik nyelvet használja a rendszer. Ezt követően a rendszer átadja az eredményeket a fő kifejezés kinyerésének és a hangulat észlelésének, figyelembe véve a nyelvet az érzelmek és kifejezések észlelése során.

Míg a nyelvi észlelési képesség a készségkészlet definiált harmadik (skill #3) képesség, a következő végrehajtandó képesség. Mivel nem blokkolja semmilyen bemenetet, az előzővel párhuzamosan fog megjelenni. Az előtte lévő felosztási képességhez hasonlóan a nyelvi észlelési képességet is egyszer kell meghívja az egyes dokumentumokhoz. A dúsítási fában már van egy új csomópont a nyelvhez.

 ![a bővítési fa a szaktudás után #2](media/cognitive-search-working-with-skillsets/enrichment-tree-skill2.png "A szaktudás #2 végrehajtása után a dúsítási fa")
 
 ### <a name="skill-3-key-phrases-skill"></a>Skill #3: a kulcsfontosságú kifejezések ismerete 

A `/document/reviews_text/pages/*` legfontosabb kifejezések ismeretének kontextusa csak egyszer lesz meghívva a gyűjtemény minden elemére `pages` . A skill kimenete a társított oldal elem alatti csomópont lesz. 

 Most már képesnek kell lennie arra, hogy megtekintse a készségkészlet képességeit, és láthatóvá tegye, hogy a dúsítások fája hogyan fog növekedni az egyes képességek végrehajtásával. Bizonyos készségek, például az egyesítési képesség és a formáló képesség, új csomópontokat is létrehozhatnak, de csak a meglévő csomópontok adatait használják, és nem hozhatók létre az új net-bővítések.

![a dúsítás fája az összes ismeret után](media/cognitive-search-working-with-skillsets/enrichment-tree-final.png "A dúsítás fája az összes ismeret után")

A fenti fában látható összekötők színei azt jelzik, hogy a dúsítások különböző képességekkel lettek létrehozva, és a csomópontokat külön kell kezelni, és nem lesznek a fölérendelt csomópont kiválasztásakor visszaadott objektum részei.

## <a name="save-enrichments"></a>Bővítések mentése

Az Azure Cognitive Searchban az indexelő elmenti az általa létrehozott kimenetet. Az egyik kimenet mindig [kereshető index](search-what-is-an-index.md). Az index megadása követelmény, és amikor csatol egy készségkészlet, az index által betöltött adatmennyiség magában foglalja a dúsítások tartalmát. Az adott képességek, például a legfontosabb kifejezések vagy az érzelmi pontszámok kimenete általában az adott célra létrehozott mezőben szerepel az indexben.

Opcionálisan egy indexelő is elküldheti a kimenetet egy [tudásbázisba](knowledge-store-concept-intro.md) más eszközökön vagy folyamatokban való felhasználásra. A rendszer a készségkészlet részeként egy tudásbázist határoz meg. Ez határozza meg, hogy a dúsított dokumentumok táblázatként vagy objektumként (fájlok vagy Blobok) vannak-e kitervezve. A táblázatos kivetítések jól illeszkednek az olyan eszközök interaktív elemzéséhez, mint például a Power BI, míg a fájlok és a Blobok jellemzően adatelemzési vagy hasonló folyamatokban használatosak. Ebben a szakaszban megtudhatja, hogyan alakíthatja ki a készségkészlet-összeállítás a projekthez használni kívánt táblákat vagy objektumokat.

### <a name="projections"></a>Leképezések

A tudásbázist tároló tartalmak esetében érdemes figyelembe venni a tartalom strukturált módját. A *kivetítés* az a folyamat, amellyel kiválaszthatja a csomópontokat a dúsítási fában, és fizikai kifejezést hozhat létre a Tudásbázisban. A kivetítések a dokumentum (tartalom és dúsítások) egyéni alakzatai, amelyek tábla-vagy objektum-kivetítésként is kiállíthatók. További információ a kivetítések használatáról: [a kivetítések használata](knowledge-store-projection-overview.md).

![Mező-hozzárendelési beállítások](./media/cognitive-search-working-with-skillsets/field-mapping-options.png "Mező-hozzárendelési beállítások a dúsítási folyamathoz")

### <a name="sourcecontext"></a>SourceContext

Az `sourceContext` elem csak a szaktudás bemenetei és a kivetítések esetében használatos. Többszintű, beágyazott objektumok létrehozásához használatos. Előfordulhat, hogy létre kell hoznia egy új objektumot, hogy beírja bemenetként egy képességbe vagy projektbe a Knowledge Store-ba. Mivel a dúsítási csomópontok nem lehetnek érvényes JSON-objektumok a dúsítási fában, és a fában lévő csomópontra hivatkoznak, csak a csomópont állapotát adja vissza a létrehozáskor, a dúsítások és a kivetítések létrehozásához pedig jól formázott JSON-objektumot kell létrehoznia. A `sourceContext` lehetővé teszi egy hierarchikus, névtelen típusú objektum létrehozását, amelyhez több ismeretre lenne szükség, ha csak a kontextust használta. 

A használata az `sourceContext` alábbi példákban látható. Tekintse meg a szakértelem kimenetét, amely a dúsítást generálta annak megállapításához, hogy az érvényes JSON-objektum-e, és nem egyszerű típus.

### <a name="slicing-projections"></a>Kivetítések szeletelése

A táblázatos kivetítési csoportok definiálásakor a dúsítási fa egyetlen csomópontja több kapcsolódó táblába is feldarabolható. Ha olyan táblát ad hozzá a forrás elérési útjához, amely egy meglévő tábla kivetítésének gyermeke, akkor az eredményül kapott alárendelt csomópont nem lesz a meglévő táblázatos leképezés gyermeke, hanem az új, kapcsolódó, táblázatba kerül. Ez a szeletelési módszer lehetővé teszi, hogy egyetlen csomópontot határozzon meg egy olyan alakzatban, amely a tábla összes vetületének forrása lehet. 

### <a name="shaping-projections"></a>Kivetítések kialakítása

A kivetítést kétféleképpen lehet definiálni:

+ A Text formáló képességgel hozzon létre egy új csomópontot, amely a kivetítéshez használt összes dúsítás legfelső szintű csomópontja. Ezután az előrejelzések szerint csak az alakzatra vonatkozó szakértelem kimenetére hivatkozhat.

+ Beágyazott alakzat használata a leképezési definíción belül.

A formáló megközelítés részletesebb, mint a beágyazott alakítás, azonban biztosítja, hogy a dúsítási fa összes mutációja a szaktudásban legyen, és hogy a kimenet egy olyan objektum, amely újra felhasználható. Ezzel szemben a beágyazott alakítás lehetővé teszi, hogy létrehozza a szükséges alakzatot, de egy névtelen objektum, és csak azokhoz a leképezésekhez érhető el, amelyekhez definiálva van. A módszerek együtt vagy külön is használhatók. A portál munkafolyamatban létrehozott készségkészlet is tartalmaz. A táblázatos kivetítésekhez egy formálói képességet használ, de a legfontosabb kifejezések táblázatának bevetítéséhez is a beágyazott alakítást használja.

A példa kiterjesztéséhez dönthet úgy, hogy eltávolítja a beágyazott formázást, és egy formáló képességgel létrehoz egy új csomópontot a legfontosabb kifejezésekhez. A `hotelReviewsDocument` `hotelReviewsPages` `hotelReviewsKeyPhrases` következő szakaszokban a két lehetőség közül választhat, hogy három táblázatba (például,, és) álló alakzatot hozzon létre.

#### <a name="shaper-skill-and-projection"></a>Formáló képesség és kivetítés

> [!Note]
> A dokumentum táblázat néhány oszlopa el lett távolítva ebből a példából a rövidség kedvéért.
>
```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "#5",
    "description": null,
    "context": "/document",
    "inputs": [        
        {
            "name": "reviews_text",
            "source": "/document/reviews_text",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "reviews_title",
            "source": "/document/reviews_title",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "AzureSearch_DocumentKey",
            "source": "/document/AzureSearch_DocumentKey",
            "sourceContext": null,
            "inputs": []
        },  
        {
            "name": "pages",
            "source": null,
            "sourceContext": "/document/reviews_text/pages/*",
            "inputs": [
                {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "LanguageCode",
                    "source": "/document/Language",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "Page",
                    "source": "/document/reviews_text/pages/*",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "keyphrase",
                    "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                    "inputs": [
                        {
                            "source": "/document/reviews_text/pages/*/Keyphrases/*",
                            "name": "Keyphrases"
                        }
                    ]
                }
            ]
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "tableprojection"
        }
    ]
}
```

A `tableprojection` fenti szakaszban meghatározott csomóponttal `outputs` mostantól a szeletelési funkciót is használhatja a `tableprojection` csomópont különböző táblákba való kivetítéséhez:

> [!Note]
> Ez csak a Knowledge Store-konfigurációban található kivetítés részlete.
>
```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsDocument",
                "generatedKeyName": "Documentid",
                "source": "/document/tableprojection"
            },
            {
                "tableName": "hotelReviewsPages",
                "generatedKeyName": "Pagesid",
                "source": "/document/tableprojection/pages/*"
            },
            {
                "tableName": "hotelReviewsKeyPhrases",
                "generatedKeyName": "KeyPhrasesid",
                "source": "/document/tableprojection/pages/*/keyphrase/*"
            }
        ]
    }
]
```

#### <a name="inline-shaping-projections"></a>Kivetítések beágyazott kialakítása

A beágyazott alakítási megközelítés nem igényel formálói képességet, mivel a kivetítésekhez szükséges összes alakzat létrehozásához szükség van. Az előző példához hasonló adatprojektek kivetítéséhez a beágyazott leképezési lehetőség a következőképpen fog kinézni:

```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsInlineDocument",
                "generatedKeyName": "Documentid",
                "sourceContext": "/document",     
                "inputs": [
                    {
                        "name": "reviews_text",
                        "source": "/document/reviews_text"
                    },
                    {
                        "name": "reviews_title",
                        "source": "/document/reviews_title"
                    },
                    {
                        "name": "AzureSearch_DocumentKey",
                        "source": "/document/AzureSearch_DocumentKey"
                    }                             
                ]
            },
            {
                "tableName": "hotelReviewsInlinePages",
                "generatedKeyName": "Pagesid",
                "sourceContext": "/document/reviews_text/pages/*",
                "inputs": [
                        {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment"
                    },
                    {
                        "name": "LanguageCode",
                        "source": "/document/Language"
                    },
                    {
                        "name": "Page",
                        "source": "/document/reviews_text/pages/*"
                    }
                ]
            },
            {
                "tableName": "hotelReviewsInlineKeyPhrases",
                "generatedKeyName": "KeyPhraseId",
                "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                "inputs": [
                    {
                        "name": "Keyphrases",
                        "source": "/document/reviews_text/pages/*/Keyphrases/*"
                    }
                ]
            }
        ]
    }
]
```
  
A megközelítések közül az egyik megfigyelés, hogy az értékek hogyan lesznek kiértékelve `"Keyphrases"` a használatával `"sourceContext"` . A `"Keyphrases"` karakterláncok gyűjteményét tartalmazó csomópont maga az oldal szövegének gyermeke. Mivel azonban a vetítésekhez JSON-objektumra van szükség, és az oldal egy primitív (karakterlánc), a a `"sourceContext"` kulcs kifejezést egy nevesített tulajdonsággal rendelkező objektumba csomagolja. Ez a módszer lehetővé teszi, hogy a primitívek egymástól függetlenül legyenek kitervezve.

## <a name="next-steps"></a>Következő lépések

A következő lépésként hozza létre az első készségkészlet kognitív ismeretekkel.

> [!div class="nextstepaction"]
> [Hozza létre az első készségkészlet](cognitive-search-defining-skillset.md).

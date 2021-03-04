---
title: AI-dúsítási fogalmak
titleSuffix: Azure Cognitive Search
description: A Content Extracting, a Natural Language Processing (NLP) és a képfeldolgozás használatával kereshető tartalmakat hozhat létre az Azure Cognitive Search indexekben az előre definiált kognitív képességekkel és az egyéni AI-algoritmusokkal.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/18/2020
ms.custom: references_regions
ms.openlocfilehash: 22d7c1bbe03d8b3c0e3b6026c9bac039f0651548
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102037251"
---
# <a name="ai-enrichment-in-azure-cognitive-search"></a>AI-gazdagítás az Azure Cognitive Search

Az AI-bővítés az [Indexelő](search-indexer-overview.md) bővítménye, amely a képekből, blobokból és egyéb strukturálatlan adatforrásokból való szöveg kinyerésére használható. A bővítés és a kivonás a tartalmat az indexelő kimeneti objektumaiban, egy [keresési indexben](search-what-is-an-index.md) vagy egy [Tudásbázisban](knowledge-store-concept-intro.md)kereshetővé teheti. 

A kinyerés és a dúsítás az indexelő által vezérelt folyamathoz csatolt *kognitív képességek* használatával valósítható meg. A Microsoft beépített képességeit használhatja, vagy külső feldolgozást ágyazhat be egy Ön által létrehozott [*Egyéni képességbe*](cognitive-search-create-custom-skill-example.md) . Egyéni képesség lehet például egy egyedi entitás modul vagy dokumentum-osztályozó, amely egy adott tartományhoz, például pénzügyi, tudományos kiadványokhoz vagy gyógyszerhez céloz.

A beépített készségek a következő kategóriákba sorolhatók: 

+ A **természetes nyelvi feldolgozási** képességek közé tartozik az [entitások felismerése](cognitive-search-skill-entity-recognition.md), a [nyelvfelismerés](cognitive-search-skill-language-detection.md), a [kulcsfontosságú kifejezés kinyerése](cognitive-search-skill-keyphrases.md), a szöveg-manipuláció, [a felismerések](cognitive-search-skill-pii-detection.md) [észlelése](cognitive-search-skill-sentiment.md)és a személyre Ezekkel a képességekkel a strukturálatlan szöveg egy indexben kereshető és szűrhető mezőkként van leképezve.

+ A **képfeldolgozási** képességek közé tartozik az [optikai karakterfelismerés (OCR)](cognitive-search-skill-ocr.md) és a [vizuális funkciók](cognitive-search-skill-image-analysis.md)azonosítása, például az arc észlelése, a képek értelmezése, a képfelismerés (a híres személyek és tereptárgyak) vagy az olyan attribútumok, mint például a képek tájolása. Ezek a képességek a képtartalom szövegének ábrázolását teszik lehetővé, így az Azure Cognitive Search lekérdezési képességeivel kereshetővé válnak.

![Dúsítási folyamat diagramja](./media/cognitive-search-intro/cogsearch-architecture.png "a dúsítási folyamat áttekintése")

Az Azure Cognitive Search beépített képességei a Cognitive Services API-k: [Computer Vision](../cognitive-services/computer-vision/index.yml) és [text Analytics](../cognitive-services/text-analytics/overview.md)előre betanított gépi tanulási modelljein alapulnak. Ha az erőforrásokat a tartalom feldolgozása során szeretné használni, csatolhatja Cognitive Services erőforrást.

A természetes nyelv és a képfeldolgozás az adatfeldolgozási fázisban történik, és az eredmények egy dokumentum összeállításának részévé válnak az Azure Cognitive Search kereshető indexében. Az adatforrások Azure-adatkészletként vannak kialakítva, majd egy indexelési folyamaton keresztül leküldve, amely a szükséges [beépített képességekkel](cognitive-search-predefined-skills.md) rendelkezik.  

## <a name="feature-availability"></a>Szolgáltatások rendelkezésre állása

Az AI-gazdagodás olyan régiókban érhető el, ahol az Azure Cognitive Services is elérhetők.  Az AI-bővítés aktuális rendelkezésre állását az [Azure-termékek régiónként elérhetővé](https://azure.microsoft.com/global-infrastructure/services/?products=search) tételét ismertető oldalon tekintheti meg.  Az AI-gazdagodás az összes támogatott régióban elérhető, kivéve a következőket:

+ Délkelet-Ausztrália
+ Észak-Kína 2
+ Kelet-Norvégia
+ Középnyugat-Németország

Ha a keresési szolgáltatás ezen régiók egyikében található, nem fogja tudni létrehozni és használni a szakértelmével, de az összes többi keresési funkció elérhető és teljes mértékben támogatott.

## <a name="when-to-use-ai-enrichment"></a>Mikor kell használni a mesterséges intelligenciát

Érdemes megfontolni a beépített kognitív ismeretek használatát, ha a nyers tartalma strukturálatlan szöveg, képtartalom vagy olyan tartalom, amely nyelvi észlelést és fordítást igényel. Ha a mesterséges intelligenciát a beépített kognitív ismeretek használatával szeretné feloldani, a keresési és adatelemzési alkalmazásaiban megnövelheti a tartalmat, és növelheti annak értékét és hasznosságát. 

Emellett érdemes lehet egyéni képességet is felvennie, ha olyan nyílt forráskódú, harmadik féltől származó vagy első féltől származó kóddal rendelkezik, amelyet be szeretne építeni a folyamatba. A különböző dokumentumtípusok legjellemzőbb jellemzőit azonosító besorolási modellek ebbe a kategóriába tartoznak, de a tartalomhoz hozzáadott bármely csomag használható.

### <a name="more-about-built-in-skills"></a>További információ a beépített képességekről

A beépített képességekkel összeállított [készségkészlet](cognitive-search-defining-skillset.md) jól illeszkedik a következő alkalmazási forgatókönyvekhez:

+ A teljes szöveges keresést végezni kívánó beolvasott dokumentumok (JPEG). A JPEG-fájlok szövegének azonosításához, kinyeréséhez és betöltéséhez egy optikai karakterfelismerési (OCR) képességet is csatolhat.

+ Kombinált képpel és szöveggel rendelkező PDF-fájlok. A PDF-fájlokban lévő szöveg kinyerhető az indexelés során a dúsítási lépések használata nélkül, de a képek és a természetes nyelvi feldolgozás hozzáadása gyakran jobb eredményt eredményezhet, mint a szabványos indexelés.

+ Többnyelvű tartalom, amelyre alkalmazni kívánja a nyelvi észlelést és a szöveges fordítást.

+ Strukturálatlan vagy részben strukturált dokumentumok, amelyek olyan tartalmat tartalmaznak, amely a nagyobb dokumentumban rejtett jelentéssel vagy kontextussal rendelkezik. 

  A Blobok gyakran tartalmaznak egy nagy méretű tartalmat, amely egyetlen "mezőre" van csomagolva. Ha rendszerképeket és természetes nyelvi feldolgozási képességeket csatol egy indexelő alkalmazáshoz, létrehozhat olyan új adatokat, amelyek a nyers tartalomban is fennmaradnak, de másképp nem különálló mezőkként. Néhány használatra kész, beépített kognitív képesség, amely segítséget nyújt a kulcsfontosságú kifejezés kinyeréséhez, a hangulat elemzéséhez és az entitások felismeréséhez (személyek, szervezetek és helyszínek).

  Emellett a beépített készségek is használhatók a tartalom átstrukturálása a szöveg felosztása, egyesítése és alakja műveletekkel.

### <a name="more-about-custom-skills"></a>További információ az egyéni képességekről

Az egyéni képességek olyan összetettebb forgatókönyveket is támogatnak, mint például az űrlapok felismerése vagy az egyéni entitások észlelése az Ön által megadott és az [Egyéni szakértelem webes felületén](cognitive-search-custom-skill-interface.md)beburkolt modell használatával. Az egyéni képességek számos példája például az [űrlap-felismerő](../cognitive-services/form-recognizer/overview.md), a [Bing Entity Search API](./cognitive-search-create-custom-skill-example.md)integrációja és az [egyéni entitások felismerése](https://github.com/Microsoft/SkillsExtractorCognitiveSearch).

## <a name="steps-in-an-enrichment-pipeline"></a>A dúsítási folyamat lépései <a name="enrichment-steps"></a>

A dúsítási folyamat [*indexeken*](search-indexer-overview.md)alapul. Az indexelő az index és az adatforrások között a dokumentum repedése között mező – mező típusú leképezések alapján tölti fel az indexet. A most már indexelt anyagokhoz csatolt ismeretek, az Ön által megadott készségkészlet (ok) szerint a dokumentumok elfogása és gazdagítása. Az indexelés után az [Azure Cognitive Search által támogatott összes lekérdezési típuson](search-query-overview.md)keresztül érheti el a tartalmat.  Ha még nem ismeri az indexeket, ez a szakasz végigvezeti a lépéseken.

### <a name="step-1-connection-and-document-cracking-phase"></a>1. lépés: a kapcsolatok és a dokumentumok repedésének fázisa

A folyamat elején strukturálatlan szöveggel vagy nem szöveges tartalommal (például képekkel, beolvasott dokumentumokkal vagy JPEG-fájlokkal) rendelkezik. Az adattáraknak olyan Azure-beli adattárolási szolgáltatásban kell lenniük, amely egy indexelő segítségével érhető el. Az indexelő "kiváló" forrás dokumentumokat gyűjthetnek a forrásadatokből származó szöveg kinyeréséhez. A dokumentum repedése az indexelés során a nem szöveges forrásokból származó szöveges tartalom kinyerésének vagy létrehozásának folyamata.

![A dokumentum repedésének fázisa](./media/cognitive-search-intro/document-cracking-phase-blowup.png "a dokumentum repedése")

 A támogatott források közé tartozik az Azure Blob Storage, az Azure Table Storage, a Azure SQL Database és az Azure Cosmos DB. A szöveges tartalmat a következő fájltípusokból lehet kinyerni: PDF-fájlok, Word-, PowerPoint-és CSV-fájlok. A teljes listát lásd: [támogatott formátumok](search-howto-indexing-azure-blob-storage.md#SupportedFormats). Az indexelés időt vesz igénybe, hogy egy kisebb, reprezentatív adatkészletet hozzon létre, majd fokozatosan felépítse a megoldás idejére.

### <a name="step-2-cognitive-skills-and-enrichment-phase"></a>2. lépés: a kognitív képességek és a gazdagodás fázisa

A gazdagodás az atomi műveleteket végző *kognitív képességekkel* történik. Ha például megrepedt egy PDF-fájlt, alkalmazhatja az entitások felismerését, a nyelvfelismerés vagy a kulcsfontosságú kifejezés kinyerését, hogy az indexben olyan új mezőket hozzon létre, amelyek nem érhetők el natív módon a forrásban. A folyamat során használt szaktudás gyűjteményét teljes egészében *készségkészlet* nevezzük.  

![Dúsítási fázis](./media/cognitive-search-intro/enrichment-phase-blowup.png "dúsítási fázis")

A készségkészlet a [beépített kognitív képességek](cognitive-search-predefined-skills.md) vagy az Ön által biztosított [egyéni képességek](cognitive-search-create-custom-skill-example.md) , valamint a készségkészlet való kapcsolódás alapján történik. A készségkészlet minimális vagy nagyon összetett lehet, és nem csak a feldolgozás típusát, hanem a műveletek sorrendjét is meghatározhatja. A készségkészlet és az indexelő részeként definiált mező-hozzárendelések teljes mértékben a dúsítási folyamatot határozzák meg. További információ ezekről a darabokról: [Készségkészlet definiálása](cognitive-search-defining-skillset.md).

Belsőleg a folyamat gazdagított dokumentumok gyűjteményét hozza létre. Eldöntheti, hogy a bővített dokumentumok mely részeit kell leképezni a keresési index indexelhető mezőihez. Ha például alkalmazta a kinyerési kifejezést és az entitás-felismerési képességeket, az új mezők a dúsított dokumentum részévé válnak, és az index mezőire képezhetők le. A bemeneti/kimeneti formációkkal kapcsolatos további tudnivalókért tekintse meg a [megjegyzéseket](cognitive-search-concept-annotations-syntax.md) .

#### <a name="add-a-knowledgestore-element-to-save-enrichments"></a>KnowledgeStore elem hozzáadása a dúsítások mentéséhez

[Keresési REST API-Version = 2020-06-30](/rest/api/searchservice/) a szakértelmével kiterjesztése olyan `knowledgeStore` definícióval, amely egy Azure Storage-kapcsolattal és-kivetítésekkel rendelkezik, amelyek leírják a dúsítások tárolási módját. Ez az indexen kívül történik. A standard AI-folyamatokban a dúsított dokumentumok átmenetiek, csak indexelés során használhatók, majd elvetették őket. A Knowledge Store-ban a dúsított dokumentumok megmaradnak. További információ: [Knowledge Store](knowledge-store-concept-intro.md).

### <a name="step-3-search-index-and-query-based-access"></a>3. lépés: a keresési index és a lekérdezésen alapuló hozzáférés

A feldolgozás befejezésekor egy olyan keresési index található, amely az Azure Cognitive Searchban teljes mértékben kereshető dokumentumokból áll. [Az index lekérdezése](search-query-overview.md) , hogy a fejlesztők és a felhasználók hogyan érhetik el a folyamat által generált dúsított tartalmat. 

![Index keresési ikonnal](./media/cognitive-search-intro/search-phase-blowup.png "Index keresési ikonnal")

Az index olyan, mint bármely más, amelyet az Azure Cognitive Search hoz létre: kiegészítheti az egyéni elemzőket, a fuzzy keresési lekérdezések meghívását, a szűrt keresés hozzáadását, illetve a keresési eredmények átalakítására szolgáló pontozási profilokkal való kísérletezést.

Az indexek olyan index-sémából jönnek létre, amely meghatározza az adott indexhez csatolt mezőket, attribútumokat és egyéb szerkezeteket, például a pontozási profilokat és a szinonimákat. Az indexek definiálása és feltöltése után a rendszer növekményes módon indexelheti az új és a frissített forrásdokumentum-dokumentumokat. Bizonyos módosítások teljes újraépítést igényelnek. Kis adatkészletet kell használnia, amíg a séma kialakítása nem stabil. A további tudnivalókért lásd az [indexek újraépítését](search-howto-reindex.md) ismertető cikket.

**Ellenőrzőlista: egy tipikus munkafolyamat**

1. Az Azure-forrásadatok részhalmaza egy reprezentatív mintának. Az indexelés időt vesz igénybe, hogy egy kisebb, reprezentatív adatkészletet hozzon létre, majd fokozatosan felépítse a megoldás idejére.

1. Hozzon létre egy [adatforrás-objektumot](/rest/api/searchservice/create-data-source) az Azure Cognitive Searchban, és adjon meg egy kapcsolódási karakterláncot az adatok lekéréséhez.

1. Hozzon létre egy [készségkészlet](/rest/api/searchservice/create-skillset) a gazdagodás lépéseivel.

1. Adja meg az [index sémát](/rest/api/searchservice/create-index). A *mezők* gyűjteménye mezőket tartalmaz a forrásadatokből. Emellett további mezőket kell kimutatnia, amelyekkel megtarthatja a gazdagodás során létrehozott tartalomhoz generált értékeket.

1. Az adatforrásra, a készségkészlet és az indexre hivatkozó [Indexelő](/rest/api/searchservice/create-indexer) definiálása.

1. Az indexelő alkalmazásban adja hozzá a *outputFieldMappings*. Ez a szakasz a készségkészlet (a 3. lépésben) lévő kimeneteket a tárgymutató sémájának bemenet mezőibe (a 4. lépésben) képezi le.

1. Küldje el az imént létrehozott *Indexer* -kérést (egy post-kérést a kérelem törzsében lévő indexelő definícióval), hogy kifejezze az indexelő az Azure Cognitive Searchban. Ez a lépés az indexelő futtatásának módja, a folyamat meghívása.

1. Lekérdezések futtatása az eredmények kiértékeléséhez és a kód módosítása a szakértelmével, a séma vagy az indexelő konfigurációjának frissítéséhez.

1. Az [Indexelő alaphelyzetbe állítása](search-howto-reindex.md) a folyamat újraépítése előtt.

## <a name="next-steps"></a>Következő lépések

+ [AI-gazdagító dokumentációs hivatkozások](cognitive-search-resources-documentation.md)
+ [Példa: egyéni képesség létrehozása AI-dúsításhoz (C#)](cognitive-search-create-custom-skill-example.md)
+ [Gyors útmutató: az AI-gazdagítás kipróbálása egy portálon](cognitive-search-quickstart-blob.md)
+ [Oktatóanyag: az AI-gazdagító API-k megismerése](cognitive-search-tutorial-blob.md)
+ [Tudástár](knowledge-store-concept-intro.md)
+ [Hozzon létre egy Knowledge Store-t a REST-ben](knowledge-store-create-rest.md)
+ [Hibaelhárítási tippek](cognitive-search-concept-troubleshooting.md)

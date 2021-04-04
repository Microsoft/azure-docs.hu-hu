---
title: A mesterséges intelligencia használata a blob tartalmának gazdagítása érdekében
titleSuffix: Azure Cognitive Search
description: Ismerje meg az Azure Cognitive Search természetes nyelvi és képelemzési képességeit, valamint azt, hogy ezek a folyamatok hogyan vonatkoznak az Azure-blobokban tárolt tartalomra.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 3d427d80e502eed0825165e640acc0755515c5b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99222048"
---
# <a name="use-ai-to-process-and-analyze-blob-content-in-azure-cognitive-search"></a>A mesterséges intelligencia használata a blob-tartalmak feldolgozásához és elemzéséhez az Azure-ban Cognitive Search

Az Azure Blob Storage-ban, amely képekből vagy hosszú, nem differenciált szövegből áll, mély tanulási elemzést végezhet az alárendelt alkalmazások számára hasznos értékes információk feltárásához és kinyeréséhez. [AI](cognitive-search-concept-intro.md)-bővítéssel a következőket teheti:

+ Képekből származó szöveg kinyerése az optikai karakterfelismeréssel (OCR)
+ Jelenet leírása vagy Címkék készítése egy fényképből
+ Nyelv felismerése és szöveg lefordítása különböző nyelvekre
+ Struktúra kikövetkeztetés az entitások felismerésével a személyek, dátumok, helyek vagy szervezetek hivatkozásainak megkeresésével

Habár előfordulhat, hogy csak az egyik ilyen AI-képességre van szüksége, közösen kombinálja őket ugyanabba a folyamatba (például a szöveg kinyerése egy beolvasott képből, majd a benne hivatkozott összes dátum és hely megkeresése). Az is gyakori, hogy az egyéni AI-vagy gépi tanulási folyamatokat élvonalbeli külső csomagok vagy házon belüli modellek formájában, valamint az Ön igényeinek megfelelően alakítottuk ki.

Bár a keresési indexelő által támogatott adatforrásokhoz is alkalmazhat mesterséges intelligenciát, a Blobok a leggyakrabban használt struktúrák a dúsítási folyamatokban. Az eredmények bekerülnek egy keresési indexbe a teljes szöveges kereséshez, vagy átirányítják az Azure Storage-ba az új alkalmazások bevezetéséhez, amely magában foglalja a felderítési vagy elemzési forgatókönyvek adatainak feltárását is. 

Ebben a cikkben egy széles lencsén keresztül tekintjük meg a mesterséges intelligenciát, hogy gyorsan fel lehessen fogni a teljes folyamatot, a blobokban lévő nyers adatok átalakításával, vagy egy keresési indexben vagy egy Tudásbázisban.

## <a name="what-it-means-to-enrich-blob-data-with-ai"></a>Mit jelent a blob-adatelemzés a mesterséges intelligenciával

Az *AI* -bővítés az Azure Cognitive Search indexelési architektúrájának része, amely a Microsofttól vagy az Ön által megadott egyéni tanulási modellekkel integrálja a gépi tanulási modelleket. Segít olyan végpontok közötti forgatókönyvek megvalósításában, amelyekben a blobokat (a meglévőket és az újakat is beleértve, vagy azok frissítését) kell feldolgoznia. a képek és a szövegek kinyeréséhez nyissa meg az összes formátumot 

A bemenetek a Blobok egyetlen tárolóban, az Azure Blob Storage-ban. A Blobok szinte bármilyen típusú szöveges vagy képi adatok lehetnek. 

A kimenet mindig keresési index, amely a gyors szöveges kereséshez, a lekérésekhez és a feltáráshoz használható az ügyfélalkalmazások számára. Emellett a kimenet olyan [*Tudásbázis*](knowledge-store-concept-intro.md) is lehet, amely az Azure-blobokban vagy az adatelemzési számítási feladatokban lévő eszközökön (például Power bi vagy adatelemző munkaterhelésekben) lévő, a kibővített dokumentumokat is kibővíti

A két között a folyamat architektúrája. A folyamat alapja az [*Indexelő*](search-indexer-overview.md), amelyhez hozzárendelhet egy [*készségkészlet*](cognitive-search-working-with-skillsets.md), amely egy vagy több, a mesterséges intelligenciát biztosító *szaktudásból* áll. A folyamat célja, hogy olyan *dúsított dokumentumokat* hozzon létre, amelyek nyers tartalomként adják meg a folyamatot, de további szerkezetet, környezetet és információt vesznek fel, miközben a folyamaton keresztül haladnak. Az indexelés során a rendszer felhasználja a bővített dokumentumokat, így fordított indexeket és más, teljes szöveges keresésben vagy feltárásban és elemzésben használt struktúrákat hozhat létre.

## <a name="required-resources"></a>Szükséges erőforrások

Az Azure Blob Storage és az Azure Cognitive Search mellett a mesterséges intelligenciát biztosító harmadik szolgáltatásra vagy mechanizmusra is szüksége lesz:

+ A beépített AI-hoz Cognitive Search az Azure Cognitive Services a látási és a természetes nyelvi feldolgozási API-kkal integrálható. Az optikai karakterfelismerés (OCR), a képelemzés vagy a természetes nyelvi feldolgozás (nyelvfelismerés, szöveg fordítása, entitások felismerése, fő kifejezés kibontása) hozzáadásához [Cognitive Services-erőforrást csatolhat](cognitive-search-attach-cognitive-services.md) . 

+ Az Azure-erőforrásokat használó egyéni AI-k olyan egyéni képességet adhatnak meg, amely a használni kívánt külső függvényt vagy modellt csomagolja. Az [egyéni képességek](cognitive-search-custom-skill-interface.md) használhatnak Azure Functions, Azure Machine learning, Azure Form-felismerő vagy más, HTTPS protokollon keresztül elérhető erőforrás által biztosított kódot.

+ Az egyéni, nem Azure AI esetében a modellnek vagy modulnak elérhetőnek kell lennie egy indexelő számára a HTTP protokollon keresztül.

Ha nem rendelkezik a rendelkezésre álló összes szolgáltatással, kezdje közvetlenül a Storage-fiók portálján. A bal oldali navigációs oldal **blob Service** területén kattintson az **Azure Cognitive Search hozzáadása** elemre egy új szolgáltatás létrehozásához, vagy válasszon ki egy meglévőt. 

Miután hozzáadta az Azure-Cognitive Search a Storage-fiókjához, az Azure-adatforrásokban lévő adatforrások standard folyamatát követheti. Javasoljuk, hogy az Azure Cognitive Searchban az **adatimportálás** VARÁZSLÓT az AI-bővítés egyszerű bevezetéséhez. A munkafolyamatban Cognitive Services-erőforrást is csatolhat. Ez a rövid útmutató végigvezeti a következő lépéseken: [AI-dúsítási folyamat létrehozása a portálon](cognitive-search-quickstart-blob.md). 

Az alábbi részek részletesebben ismertetik az összetevőket és a munkafolyamatokat.

## <a name="use-a-blob-indexer"></a>BLOB-indexelő használata

Az AI-bővítés egy indexelési folyamat bővítménye, és az Azure Cognitive Searchban ezek a folyamatok egy *Indexelő* fölé épülnek. Az indexelő egy adatforrást támogató alszolgáltatás, amely belső logikával rendelkezik a mintavételi adatokhoz, a metaadatok beolvasásához, az adatok lekéréséhez és az adatok natív formátumokból való szerializálásához a további importáláshoz. Az indexelő gyakran saját maguk használják az importáláshoz, elkülönítve az AI-től, de ha mesterséges intelligenciát szeretne létrehozni, szüksége lesz egy indexelő és egy készségkészlet. Ez a szakasz kiemeli az Indexelő; a következő szakasz a szakértelmével koncentrál.

Az Azure Storage-beli Blobok indexelése a [blob indexelő](search-howto-indexing-azure-blob-storage.md)használatával történik. Az indexelő az **adatimportálás** varázsló, egy REST API vagy egy SDK használatával hívható meg. A rendszer meghívja a blob-indexelő, ha az indexelő által használt adatforrás egy Azure Blob-tároló. A Blobok egy részhalmazának indexeléséhez létrehozhat egy virtuális könyvtárat, amelyet aztán paraméterként adhat át, vagy szűrheti a fájltípusok kiterjesztését.

Az indexelő a "dokumentum repedése", a Blobok megnyitása a tartalom vizsgálatához. Az adatforráshoz való csatlakozás után ez a folyamat első lépése. A blob-adatok esetében itt a PDF, az Office-dokumentumok, a képek és más tartalomtípusok észlelhetők. A szöveg kibontásával nem számítunk fel díjat. A képek kinyerésével kapcsolatos nagy mennyiségű dokumentum díjait a [díjszabás lapon](https://azure.microsoft.com/pricing/details/search/)találja.

Bár az összes dokumentum meg lesz repedt, a dúsítás csak akkor történik meg, ha explicit módon megadja a szükséges képességeket. Ha például a folyamat kizárólag képelemzésből áll, akkor a rendszer figyelmen kívül hagyja a tárolóban vagy a dokumentumokban lévő szöveget.

A blob indexelő konfigurációs paramétereket tartalmaz, és támogatja a változások követését, ha az alapul szolgáló adatok elegendő információt biztosítanak. A [blob-indexelő konfigurálásával](search-howto-indexing-azure-blob-storage.md)kapcsolatban bővebben is tájékozódhat.

## <a name="add-ai-components"></a>AI-összetevők hozzáadása

Az AI-dúsítás olyan modulokra utal, amelyek mintákat vagy tulajdonságokat keresnek, majd ennek megfelelően végrehajtják a műveletet. Az Arcfelismerés a fényképekben, a fényképek szöveges leírásában, a dokumentumokban található kulcsfontosságú kifejezések észlelésében, valamint az OCR (vagy a nyomtatott vagy kézírásos szöveg felismerése bináris fájlokban) szemléltető példákat mutat be.

Az Azure Cognitive Searchban a *képességek* a mesterséges intelligencia-feldolgozás egyes összetevői, amelyek önállóan vagy más képességekkel kombinálva is használhatók. 

+ A beépített képességeket Cognitive Services támogatja, és a képek elemzése a Computer Visionon alapul, és a természetes nyelvi feldolgozás a Text Analytics alapján történik. A teljes lista a tartalom- [gazdagítás beépített képességei](cognitive-search-predefined-skills.md)című témakörben található.

+ Az egyéni képességek egyéni kódok, amelyek egy olyan [illesztőfelület-definícióba](cognitive-search-custom-skill-interface.md) vannak becsomagolva, amely lehetővé teszi az integrációt a folyamatba. Az ügyfél-megoldásokban általános gyakorlat, hogy mindkettőt a nyílt forráskódú, harmadik féltől származó vagy az első féltől származó AI-modulokat biztosító egyéni ismeretekkel együtt használja.

A *készségkészlet* a folyamatokban használt szaktudás gyűjteménye, amely a dokumentum repedési fázisának meghívása után válik elérhetővé. Az indexelő pontosan egy készségkészlet tud felhasználni, de a készségkészlet egy indexelő függetlenül létezik, így más forgatókönyvekben is felhasználható.

Az egyéni képességek bonyolultak lehetnek, de a megvalósítás szempontjából egyszerűek és egyszerűen megtekinthetők. Ha olyan meglévő csomagokat használ, amelyek minta-megfeleltetési vagy besorolási modelleket biztosítanak, a blobokból kinyert tartalmat átadhatja a feldolgozásra. Mivel az AI-bővítés Azure-alapú, a modellnek az Azure-on is kell lennie. Néhány gyakori üzemeltetési módszer [Azure functions](cognitive-search-create-custom-skill-example.md) vagy [tárolók](https://github.com/Microsoft/SkillsExtractorCognitiveSearch)használatát is magában foglalja.

A Cognitive Services által támogatott beépített készségekhez szükség van egy [csatolt Cognitive Servicesra](cognitive-search-attach-cognitive-services.md) , amely hozzáférést biztosít az erőforráshoz. Egy teljes körű kulcs a képek elemzéséhez, a nyelvfelismerés, a szöveg fordításához és a szöveges elemzésekhez. Az egyéb beépített képességek az Azure Cognitive Search funkciói, és nem igényelnek további szolgáltatást vagy kulcsot. A Text formáló, a Splitter és az egyesítés például olyan segítő képességekre mutat, amelyek néha szükségesek a folyamat tervezésekor.

Ha kizárólag egyéni képességeket és beépített segédprogram-képességeket használ, akkor nincs Cognitive Serviceshoz kapcsolódó függőség vagy költség.

## <a name="consume-ai-enriched-output-in-downstream-solutions"></a>Mesterséges intelligenciát használó kimenet felhasználása az alsóbb rétegbeli megoldásokban

Az AI-bővítés kimenete az Azure-Cognitive Search keresési indexe vagy az Azure Storage-ban található [Tudásbázis](knowledge-store-concept-intro.md) .

Az Azure Cognitive Searchban egy keresési indexet használunk az interaktív feltáráshoz, amely ingyenes szöveges és szűrt lekérdezéseket használ egy ügyfélalkalmazás számára. Az AI-n keresztül létrehozott bővített dokumentumok JSON formátumban vannak formázva, és ugyanúgy vannak indexelve, mint az összes dokumentum az Azure Cognitive Search-ban, az indexelő által biztosított összes előnyt kihasználva. Az indexelés során például a blob indexelő a konfigurációs paraméterekre és beállításokra hivatkozik bármely mező-hozzárendelés vagy az észlelési logika kihasználása érdekében. Ezek a beállítások teljes mértékben elérhetők a rendszeres indexeléshez és a mesterséges intelligenciával bővített számítási feladatokhoz. Az indexelés után, ha a tartalom az Azure Cognitive Searchon van tárolva, gazdag lekérdezéseket hozhat létre, és szűrheti a tartalmakat a tartalom megismerése érdekében.

Az Azure Storage-ban a Knowledge Store két megnyilvánulással rendelkezik: egy blob-tárolóval vagy a Table Storage tábláival. 

+ A blob-tárolók teljes egészében rögzítik a dúsított dokumentumokat, ami akkor lehet hasznos, ha más folyamatokra szeretne csatlakozni. 

+ Ezzel szemben a Table Storage a dúsított dokumentumok fizikai kivetítéseit is képes kezelni. Létrehozhat szeleteket vagy olyan dúsított dokumentumok rétegeit, amelyek meghatározott részeket tartalmaznak vagy kizárhatnak. A Power BI elemzéséhez az Azure Table Storage táblái az adatforrások lesznek a további vizualizációk és feltárások számára.

A folyamat végén található dúsított dokumentumok eltérnek az eredeti bemeneti verziótól, mert további, a dúsítás során kinyert vagy létrehozott adatokat tartalmazó mezők jelennek meg. Így az eredeti és a létrehozott tartalom kombinációjával is dolgozhat, függetlenül attól, hogy melyik kimeneti struktúrát használja.

## <a name="next-steps"></a>Következő lépések

A mesterséges intelligenciával sokkal többet is megtudhat az Azure Storage-ban tárolt adatairól, többek között a Cognitive Services különböző módokon történő kombinálásával, valamint egyéni ismeretek készítésével olyan esetekre, amikor a forgatókönyvhöz nem áll rendelkezésre kognitív szolgáltatás. Az alábbi hivatkozásokra kattintva többet is megtudhat.

+ [Blobok feltöltése, letöltése és listázása a Azure Portal (Azure Blob Storage)](../storage/blobs/storage-quickstart-blobs-portal.md)
+ [BLOB-indexelő beállítása (Azure Cognitive Search)](search-howto-indexing-azure-blob-storage.md) 
+ [AI-dúsítás áttekintése (Azure Cognitive Search)](cognitive-search-concept-intro.md) 
+ [Készségkészlet létrehozása (Azure Cognitive Search)](cognitive-search-defining-skillset.md)
+ [Csomópontok leképezése egy jegyzet fájában (Azure Cognitive Search)](cognitive-search-output-field-mapping.md)
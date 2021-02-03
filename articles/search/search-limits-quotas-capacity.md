---
title: A csomagokra és az SKU-ra vonatkozó szolgáltatási korlátok
titleSuffix: Azure Cognitive Search
description: A kapacitás megtervezésére és az Azure-Cognitive Search kéréseire adott válaszok maximális korlátozására használt szolgáltatási korlátok.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 994ed74750d159dfdb83259e9fe921f870ec2241
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509367"
---
# <a name="service-limits-in-azure-cognitive-search"></a>Az Azure Cognitive Search szolgáltatási korlátai

A tárolás, a munkaterhelések és az indexek és egyéb objektumok mennyiségére vonatkozó maximális korlátok attól függnek, hogy az Azure Cognitive Search **ingyenes**, **alapszintű**, **standard** vagy **Storage optimalizált** árképzési szinteken van-e [kiépítve](search-create-service-portal.md) .

+ Az **ingyenes** egy több-bérlős megosztott szolgáltatás, amely az Azure-előfizetéshez tartozik. 

+ Az **alapszintű** megoldás dedikált számítástechnikai erőforrásokat biztosít az éles számítási feladatokhoz, de bizonyos hálózati infrastruktúrát más bérlők is megosztanak.

+ A **standard** szintű dedikált gépeken fut, és minden szinten nagyobb a tárterület és a feldolgozási kapacitás. A standard szint négy szinten érhető el: S1, S2, S3 és S3 HD. Az S3 nagy sűrűségű (S3 HD) a [több-bérlős](search-modeling-multitenant-saas-applications.md) és nagy mennyiségű kisméretű indexhez (szolgáltatás: 3000 indexek) van tervezve. Az S3 HD nem biztosítja az [Indexelő szolgáltatást](search-indexer-overview.md) , és az adatfeldolgozásnak olyan API-kat kell használnia, amelyek a forrástól az indexig leküldik az adatot. 

+ A Storage-ra **optimalizált tárolók** a **standard szintű** tárterületet, tárolási sávszélességet és memóriát biztosító dedikált gépeken futnak. Ez a réteg nagy, lassan változó indexeket céloz meg. A tároló optimalizált két szinten érhető el: L1 és L2.

## <a name="subscription-limits"></a>Előfizetés korlátai
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>Tárolási korlátok
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>Index korlátai

| Erőforrás | Ingyenes | 1. alapszintű &nbsp; <sup></sup>  | S1 | S2 | S3 | S3 &nbsp; HD | L1 | L2 |
| -------- | ---- | ------------------- | --- | --- | --- | --- | --- | --- |
| Indexek maximális száma |3 |5 vagy 15 |50 |200 |200 |1000 partíciónként vagy 3000 szolgáltatásonként |10 |10 |
| Egyszerű mezők maximális száma index szerint |1000 |100 |1000 |1000 |1000 |1000 |1000 |1000 |
| Összetett gyűjtemény mezőinek maximális száma index szerint |40 |40 |40 |40 |40 |40 |40 |40 |
| A 2. dokumentumba tartozó összes összetett gyűjteményen belüli elemek maximális száma &nbsp; <sup></sup> |3000 |3000 |3000 |3000 |3000 |3000 |3000 |3000 |
| Összetett mezők maximális mélysége |10 |10 |10 |10 |10 |10 |10 |10 |
| Mutatók maximális száma index [alapján](/rest/api/searchservice/suggesters) |1 |1 |1 |1 |1 |1 |1 |1 |
| [Pontozási profilok](/rest/api/searchservice/add-scoring-profiles-to-a-search-index) maximális száma index szerint |100 |100 |100 |100 |100 |100 |100 |100 |
| Függvények maximális száma profilban |8 |8 |8 |8 |8 |8 |8 |8 |

<sup>1</sup> a december 2017-ig létrehozott alapszintű szolgáltatások alacsonyabb korláttal rendelkeznek (15 helyett 5) az indexeken. Az alapszintű csomag az egyetlen olyan SKU, amelynek alsó korlátja a 100 mező/index.

<sup>2</sup> egy felső korlát létezik az elemekhez, mert nagy mennyiségű, jelentősen növeli az indexhez szükséges tárhelyet. Egy összetett gyűjtemény egy eleme a gyűjtemény tagjaként van definiálva. Tegyük fel például, hogy egy [szállodai dokumentum egy Rooms Complex-gyűjteménysel van ellátva](search-howto-complex-data-types.md#indexing-complex-types), a szobák gyűjtemény minden helyisége elemnek minősül. Az indexelés során az indexelési motor a teljes dokumentumon belül legfeljebb 3000 elemet tud biztonságosan feldolgozni. [Ez a korlát](search-api-migration.md#upgrade-to-2019-05-06) a ben jelent meg, `api-version=2019-05-06` és csak az összetett gyűjteményekre vonatkozik, nem karakterlánc-gyűjteményekbe vagy összetett mezőkbe.

<a name="document-limits"></a>

## <a name="document-limits"></a>Dokumentumok korlátai 

Október 2018-tól kezdve a dokumentumok száma megszűnik bármely olyan új szolgáltatás esetében, amelyet bármely számlázandó rétegben (alapszintű, S1, S2, S3, S3 HD) hoztak létre bármely régióban. A 2018 október előtt létrehozott régebbi szolgáltatások továbbra is szerepelhetnek a dokumentumok számának korlátaiban.

Annak megállapításához, hogy a szolgáltatás rendelkezik-e dokumentum-korlátozásokkal, használja a [szolgáltatás statisztikáinak Beolvasása REST API](/rest/api/searchservice/get-service-statistics). A dokumentum korlátai a válaszban láthatók, a `null` korlátozás nélkül.

> [!NOTE]
> Habár a szolgáltatás nem korlátozza a dokumentumokra vonatkozó korlátozásokat, az alapszintű, S1, S2 és S3 keresési szolgáltatások esetében az index körülbelül 24 000 000 000 dokumentumra van korlátozva. Az S3 HD esetében a szegmens korlátja 2 000 000 000 dokumentum/index. Egy összetett gyűjtemény minden eleme külön dokumentumnak számít a szilánkok korlátaira vonatkozóan.

### <a name="document-size-limits-per-api-call"></a>Dokumentum méretének korlátai API-hívás szerint

Az index API meghívásakor a dokumentum maximális mérete körülbelül 16 megabájt.

A dokumentum mérete valójában korlátozza az index API-kérés törzsének méretét. Mivel egyszerre több dokumentumot is át lehet adni az index API-hoz, a méretkorlát reálisan függ attól, hogy hány dokumentum van a kötegben. Egyetlen dokumentummal rendelkező kötegek esetében a dokumentum maximális mérete 16 MB JSON.

A dokumentum méretének becslése során ne feledje, hogy csak azokat a mezőket veszi figyelembe, amelyeket egy keresési szolgáltatás felhasználhat. A forrásbizonylatok bináris vagy képi adatait ki kell hagyni a számításokból.  

## <a name="indexer-limits"></a>Indexelő korlátai

A maximális futási idő a szolgáltatás egészére vonatkozó egyensúlyt és stabilitást biztosít, de a nagyobb adatkészletek esetében a maximálisan megengedettnél több indexelési időt is igénybe vehet. Ha egy indexelési feladatok nem hajthatók végre a maximálisan engedélyezett időtartamon belül, futtassa az ütemezett futtatást. Az ütemező nyomon követi az indexelési állapotot. Ha egy ütemezett indexelési feladatot valamilyen oknál fogva megszakítanak, az indexelő kiválaszthatja, hogy a következő ütemezett futtatásnál melyik utolsó marad.


| Erőforrás | Ingyenes &nbsp; <sup>1</sup> | Alapszintű &nbsp; <sup>2</sup>| S1 | S2 | S3 | S3 &nbsp; HD &nbsp; <sup>3</sup>|L1 |L2 |
| -------- | ----------------- | ----------------- | --- | --- | --- | --- | --- | --- |
| Indexelők maximális száma |3 |5 vagy 15|50 |200 |200 |N/A |10 |10 |
| Adatforrások maximális száma |3 |5 vagy 15 |50 |200 |200 |N/A |10 |10 |
| Maximális szakértelmével <sup>4</sup> |3 |5 vagy 15 |50 |200 |200 |N/A |10 |10 |
| Az indexelési terhelés maximális száma hívás közben |10 000 dokumentum |Csak a dokumentumok maximális száma korlátozza |Csak a dokumentumok maximális száma korlátozza |Csak a dokumentumok maximális száma korlátozza |Csak a dokumentumok maximális száma korlátozza |N/A |Korlátlan |Korlátlan |
| Minimális ütemterv | 5 perc |5 perc |5 perc |5 perc |5 perc |5 perc |5 perc | 5 perc |
| Maximális futási idő| 1-3 perc |24 óra |24 óra |24 óra |24 óra |N/A  |24 óra |24 óra |
| Az indexek maximális futási ideje készségkészlet <sup>5</sup> | 3-10 perc |2 óra |2 óra |2 óra |2 óra |N/A  |2 óra |2 óra |
| BLOB indexelő: blob maximális mérete (MB) |16 |16 |128 |256 |256 |N/A  |256 |256 |
| BLOB indexelő: blobból kinyert tartalom maximális száma |32 000 |64 000 |4 &nbsp; millió |8 &nbsp; millió |16 &nbsp; millió |N/A |4 &nbsp; millió |4 &nbsp; millió |

<sup>1</sup> az ingyenes szolgáltatások esetében az indexelő maximális végrehajtási ideje 3 perc a blob-forrásokhoz és 1 perc az összes többi adatforráshoz. A Cognitive Servicesba beérkező AI-indexelés esetén az ingyenes szolgáltatások napi 20 ingyenes tranzakcióra korlátozódnak, ahol a tranzakciót olyan dokumentumként határozzák meg, amely sikeresen áthalad a dúsítási folyamaton.

<sup>2</sup> a 2017 decembere előtt létrehozott alapszintű szolgáltatások alacsonyabb korláttal rendelkeznek (15 helyett 5) az indexelő, az adatforrások és a szakértelmével esetében.

<sup>3</sup> az S3 HD-szolgáltatások nem tartalmazzák az indexelő támogatását.

<sup>4</sup> készségkészlet legfeljebb 30 ismeret.

<sup>5</sup> az AI-bővítés és-rendszerkép elemzése számítási igényű, és aránytalanul nagy mennyiségű rendelkezésre álló feldolgozási kapacitást igényel. A számítási feladatokhoz tartozó futási idő lerövidítve lett, hogy a várólistán lévő többi feladat több lehetőséget biztosítson a futtatásra.

> [!NOTE]
> Ahogy az [index korlátaiban](#index-limits)is említettük, az indexek az összes összetett gyűjteményre vonatkozó, a legújabb GA API-verziótól kezdődően a felső 3000 korlátot is kikényszerítik az összes, a ( `2019-05-06` Ez azt jelenti, hogy ha az indexelő korábbi API-verzióval hozta létre, a korlát nem vonatkozik rá. A maximális kompatibilitás megőrzése érdekében egy korábbi API-verzióval létrehozott indexelő, majd egy API-verzióval `2019-05-06` vagy később frissülve a rendszer továbbra is **kizárja** a korlátokat. Az ügyfeleknek tisztában kell lenniük azzal, hogy milyen negatív hatással vannak a nagyon nagy összetett gyűjtemények (a korábban említettek szerint), és javasoljuk, hogy hozzon létre minden új indexelő a legújabb GA API-verzióval.

## <a name="shared-private-link-resource-limits"></a>Megosztott magánhálózati kapcsolati erőforrások korlátai

Az indexelő más Azure-erőforrásokhoz is hozzáférhetnek a [megosztott privát kapcsolati erőforrás API](/rest/api/searchmanagement/sharedprivatelinkresources)-n keresztül kezelt [privát végpontokon](search-indexer-howto-access-private.md) keresztül. Ez a szakasz az ehhez a képességhez kapcsolódó korlátokat ismerteti.

| Erőforrás | Ingyenes | Alapszintű | S1 | S2 | S3 | S3 HD | L1 | L2
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Privát végpont-indexelő támogatása | Nem | Igen | Igen | Igen | Igen | Nem | Igen | Igen |
| Privát végpontok támogatása az indexek számára a készségkészlet<sup>1</sup> | Nem | Nem | Nem | Igen | Igen | Nem | Igen | Igen |
| Privát végpontok maximális száma | N/A | 10 vagy 30 | 100 | 400 | 400 | N/A | 20 | 20 |
| Maximális eltérő erőforrástípusok<sup>2</sup> | N.A. | 4 | 7 | 15 | 15 | N.A. | 4 | 4 |

<sup>1</sup> a mesterséges intelligencia és a képek elemzése számítási igényű, és aránytalanul nagy mennyiségű rendelkezésre álló feldolgozási kapacitást igényel. Emiatt a magánhálózati kapcsolatok le vannak tiltva az alacsonyabb szinten, hogy elkerülje a keresési szolgáltatás teljesítményére és stabilitására gyakorolt negatív hatást.

<sup>2</sup> a különböző erőforrástípusok számát a rendszer az `groupId` adott keresési szolgáltatás összes megosztott magánhálózati kapcsolati erőforrásában használt egyedi értékek számaként számítja ki, az erőforrás állapotától függetlenül.

## <a name="synonym-limits"></a>Szinonimák korlátai

A szinonimák leképezésének maximális száma a réteg szerint változhat. Minden egyes szabály legfeljebb 20 kiterjesztéssel rendelkezhet, ahol a terjeszkedés egy egyenértékű kifejezés. Például a "Cat", a "Kitty", a "macskaféle" és az "Felis" (a macskák nemhez tartozó) társítása 3 kiterjesztésnek számít.

| Erőforrás | Ingyenes | Alapszintű | S1 | S2 | S3 | S3 – HD |L1 | L2 |
| -------- | -----|------ |----|----|----|-------|---|----|
| Szinonimák maximális térképe |3 |3|5 |10 |20 |20 | 10 | 10 |
| Szabályok maximális száma leképezés szerint |5000 |20000|20000 |20000 |20000 |20000 | 20000 | 20000  |

## <a name="data-limits-ai-enrichment"></a>Adatkorlátok (AI-dúsítás)

Egy [mesterséges intelligencia](cognitive-search-concept-intro.md) -bővítési folyamat, amely egy Text Analytics erőforrást hív meg az [entitások felismeréséhez](cognitive-search-skill-entity-recognition.md), a [kulcs kifejezésének kinyeréséhez](cognitive-search-skill-keyphrases.md), a [hangulat elemzéséhez](cognitive-search-skill-sentiment.md), a [nyelvfelismerés](cognitive-search-skill-language-detection.md)és a [személyes adatok észleléséhez](cognitive-search-skill-pii-detection.md) , az adatkorlátozások hatálya alá esik. A rekordok maximális méretének 50 000 karakternek kell lennie, a következőképpen mérve: [`String.Length`](/dotnet/api/system.string.length) . Ha meg kell szakítania az adatait, mielőtt elküldené az érzelmeket elemző eszköznek, használja a [szöveg felosztása készséget](cognitive-search-skill-textsplit.md).

## <a name="throttling-limits"></a>Szabályozási korlátok

Az API-kérelmeket a rendszer a maximális kapacitásnak megfelelően szabályozza. A szabályozás eltérően viselkedik a különböző API-kkal. A szolgáltatás terhelése alapján a lekérdezési API-k (keresés/javaslat/automatikus kiegészítés) és az indexelő API-k szabályozása dinamikusan történik. Az API-k és a Service Operations API indexe statikus kérelmekre vonatkozó korlátozásokat tartalmaz. 

Az indexhez kapcsolódó műveletekre vonatkozó statikus díjszabási kérelmek:

+ Indexek listázása (/Indexes beolvasása): másodpercenként 3/keresési egység
+ Index beolvasása (/Indexes/myindex beolvasása): másodpercenként 10/keresési egység
+ Index létrehozása (/Indexes POST): 12 percenként, keresési egységenként
+ Index létrehozása vagy frissítése (PUT/Indexes/myindex): 6 másodpercenként/keresési egységenként
+ Index törlése (/Indexes/myindex törlése): 12 percenként/keresési egységenként 

A szolgáltatással kapcsolatos műveletek statikus díjszabási korlátja:

+ Szolgáltatási statisztika (GET/servicestats): másodpercenként 4 másodpercenként

## <a name="api-request-limits"></a>API-kérelmek korlátai
* Legfeljebb 16 MB/kérelem <sup>1</sup>
* Legfeljebb 8 KB URL-cím hossza
* Maximális 1000 dokumentum/köteg/index feltöltés, Egyesítés vagy törlés
* Maximális 32 mező a $orderby záradékban
* A maximális keresési kifejezés mérete 32 766 bájt (32 KB mínusz 2 bájt) UTF-8 kódolású szöveg esetén

<sup>1</sup> az Azure Cognitive Search-ban a kérelem törzse 16 MB-os felső korláttal rendelkezik, és az egyes mezők vagy gyűjtemények esetében gyakorlati korlátozást ír elő, amely nem korlátozza az elméleti korlátokat (lásd a [támogatott adattípusokat](/rest/api/searchservice/supported-data-types) a mezők összeállításával és korlátozásával kapcsolatos további információkért).

## <a name="api-response-limits"></a>API-válaszok korlátai
* A keresési eredmények által visszaadott maximális 1000-dokumentumok száma
* A javasolt API-kérések száma legfeljebb 100 javaslatot ad vissza

## <a name="api-key-limits"></a>API-kulcsok korlátai
Az API-kulcsok a szolgáltatás-hitelesítéshez használatosak. Két típusukat különböztetjük meg. A felügyeleti kulcsok a kérelem fejlécében vannak megadva, és teljes írási és olvasási hozzáférést biztosítanak a szolgáltatáshoz. A lekérdezési kulcsok csak olvashatók, az URL-címen vannak megadva, és általában az ügyfélalkalmazások számára vannak elosztva.

* Maximum 2 rendszergazdai kulcs/szolgáltatás
* Szolgáltatáson legfeljebb 50 lekérdezési kulcs

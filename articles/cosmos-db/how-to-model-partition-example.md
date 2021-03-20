---
title: Azure Cosmos DB-modell és-particionálás valós példával
description: Ismerje meg, hogyan modellezheti és particionálhatja a valós példákat a Azure Cosmos DB Core API használatával
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/23/2019
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: d2f35ae7a6110acb2ca89bdaeb487eddabf84923
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98185818"
---
# <a name="how-to-model-and-partition-data-on-azure-cosmos-db-using-a-real-world-example"></a>Adatok modellezése és particionálása az Azure Cosmos DB-ben való életből vett példa használatával
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ez a cikk számos olyan Azure Cosmos DB-fogalomra épül, mint például [az adatmodellezés](modeling-data.md), a [particionálás](partitioning-overview.md)és a [kiosztott átviteli sebesség](request-units.md) , hogy bemutassa, hogyan kezelhető a valós adattervezési gyakorlat.

Ha általában a kapcsolatok adatbázisaival dolgozik, valószínűleg az adatmodell kialakításával kapcsolatos szokásait és tapasztalatait is felkészítette. Az egyes megkötések, de a Azure Cosmos DB egyedi erőssége miatt az ajánlott eljárások többsége nem jól lefordítható, és a legoptimálisabb megoldásokra is húzhatja őket. Ennek a cikknek a célja, hogy végigvezeti Önt a valós használati eset modellezésének teljes folyamatán a Azure Cosmos DBon, az elemek modellezése és a tárolók particionálása között.

[Töltse le vagy tekintse meg](https://github.com/jwidmer/AzureCosmosDbBlogExample) a jelen cikkben szereplő fogalmakat bemutató, Közösség által generált forráskódot. Ezt a kódrészletet a közösségi közreműködők járultak hozzá, és Azure Cosmos DB csapat nem támogatja a karbantartását.

## <a name="the-scenario"></a>Esetleírás

Ebben a gyakorlatban egy olyan blogging platform tartományát fogjuk figyelembe venni, ahol a *felhasználók* létrehozhatnak *bejegyzéseket*. *A felhasználók emellett* hozzáadhatnak *megjegyzéseket* a bejegyzésekhez.

> [!TIP]
> Kiemeltek néhány szót *dőlt betűvel*; Ezek a szavak határozzák meg, hogy milyen típusú "dolgok" a modellnek.

További követelmények hozzáadása a specifikációhoz:

- A lap megjeleníti a nemrég létrehozott bejegyzések hírcsatornáját,
- Egy adott felhasználó összes bejegyzését beolvashatja, a posthoz tartozó összes megjegyzést és az összes,
- A bejegyzéseket a szerzők felhasználónevével, valamint a hozzászólások számával és a kívánt értékekkel együtt adja vissza.
- A megjegyzéseket és a szereteket is visszaadja a felhasználók felhasználónevével, akik létrehozták azokat.
- A hozzászólások csak a tartalmuk lerövidített összegzését mutatják be a listában.

## <a name="identify-the-main-access-patterns"></a>A fő hozzáférési minták azonosítása

Első lépésként a megoldás hozzáférési mintáinak azonosításával egy struktúrát adunk a kezdeti specifikációnk számára. Azure Cosmos DB adatmodell tervezésekor fontos tisztában lennie azzal, hogy a modellnek milyen kéréseket kell biztosítania ahhoz, hogy a modell hatékonyan szolgálja majd ki a kérelmeket.

Ahhoz, hogy az átfogó folyamat könnyebben követhető legyen, a különböző kérelmeket parancsok vagy lekérdezések szerint kategorizáljuk, a [CQRS](https://en.wikipedia.org/wiki/Command%E2%80%93query_separation#Command_query_responsibility_segregation) , ahol a parancsok írási kérések (azaz a rendszer frissítésének szándéka), és a lekérdezések csak olvasható kérelmek.

A platform által elérhető kérelmek listája a következő:

- **[C1]** Felhasználó létrehozása/szerkesztése
- **[Q1]** Felhasználó beolvasása
- **[C2]** Bejegyzés létrehozása/szerkesztése
- **[Q2]** Bejegyzés beolvasása
- **[Q3]** A felhasználó bejegyzéseinek rövid formában való listázása
- **[C3]** Megjegyzés létrehozása
- **[Q4]** Bejegyzés megjegyzésének listázása
- **[C4]** Mint egy post
- **[Q5]** A post kedvelt listájának listázása
- **[K6]** A rövid formátumban létrehozott *x* legújabb bejegyzések listázása (hírcsatorna)

Ebben a szakaszban még nem gondoltuk, hogy az egyes entitások (felhasználók, bejegyzések stb.) mely adatai lesznek benne. Ez a lépés általában az elsők között a rokoni tárolók kialakításakor szükséges, mert meg kell állapítani, hogy az entitások hogyan lesznek lefordítva táblák, oszlopok, idegen kulcsok stb. alapján. Sokkal kevésbé fontos, hogy egy dokumentum-adatbázis ne kényszerítse a sémákat íráskor.

A fő ok, amiért fontos, hogy az elejétől kiderítse a hozzáférési mintákat, azért, mert a kérések listája a test Suite lesz. Minden alkalommal, amikor megismétli az adatmodellt, áttekintjük a kérelmeket, és megvizsgáljuk a teljesítményt és a méretezhetőséget. Kiszámítjuk az egyes modellekben felhasznált kérelmek egységeit, és optimalizáljuk azokat. Az összes modell az alapértelmezett indexelési házirendet használja, és a megadott tulajdonságok indexelésével felülbírálhatja azt, ami tovább növelheti az RU felhasználását és késését.

## <a name="v1-a-first-version"></a>V1: első verzió

Kezdjük két tárolóval: `users` és `posts` .

### <a name="users-container"></a>Felhasználók tárolója

Ez a tároló csak a felhasználói elemeket tárolja:

```json
{
    "id": "<user-id>",
    "username": "<username>"
}
```

Ezt a tárolót a alapján particionáljuk `id` , ami azt jelenti, hogy az adott tárolón belüli minden logikai partíció csak egyetlen elemből fog állni.

### <a name="posts-container"></a>Bejegyzések tárolója

Ez a tároló bejegyzéseket, megjegyzéseket és a következőt szereti:

```json
{
    "id": "<post-id>",
    "type": "post",
    "postId": "<post-id>",
    "userId": "<post-author-id>",
    "title": "<post-title>",
    "content": "<post-content>",
    "creationDate": "<post-creation-date>"
}

{
    "id": "<comment-id>",
    "type": "comment",
    "postId": "<post-id>",
    "userId": "<comment-author-id>",
    "content": "<comment-content>",
    "creationDate": "<comment-creation-date>"
}

{
    "id": "<like-id>",
    "type": "like",
    "postId": "<post-id>",
    "userId": "<liker-id>",
    "creationDate": "<like-creation-date>"
}
```

Ezt a tárolót a használatával particionáljuk `postId` , ami azt jelenti, hogy az adott tárolón belüli minden logikai partíció egy bejegyzést tartalmaz, a bejegyzés összes megjegyzését és a bejegyzéshez tartozó összes hozzászólást.

Vegye figyelembe, hogy az ebben a `type` tárolóban tárolt elemekben bevezetünk egy tulajdonságot a tároló által üzemeltetett entitások három típusa közötti különbségtételhez.

Azt is kiválasztottuk, hogy a kapcsolódó adatokra hivatkozzon a beágyazás helyett ( [ezt a szakaszt](modeling-data.md) a fogalmakkal kapcsolatos részletekért tekintse meg), mivel:

- nincs felső korlát ahhoz, hogy a felhasználók hány hozzászólást hozhatnak létre,
- a bejegyzések önkényesen hosszúak lehetnek,
- nincs felső korlát arra vonatkozóan, hogy hány megjegyzést és szeret egy hozzászólást,
- azt szeretnénk, hogy a bejegyzés frissítése nélkül is hozzá lehessen adni egy hozzászólást vagy egy hasonlót a bejegyzéshez.

## <a name="how-well-does-our-model-perform"></a>Milyen jól teljesíti a modelljét?

Az első verzió teljesítményének és méretezhetőségének felmérése most már ideje. Minden korábban azonosított kérelem esetében mérjük a késését, és azt, hogy hány kérelem-egységet használ fel. Ez a mérés olyan próbabábu-adatkészleten történik, amely egy 100 000 felhasználót tartalmaz, amely 5 – 50 bejegyzést tesz közzé felhasználónként, és legfeljebb 25 megjegyzést és 100-ot.

### <a name="c1-createedit-a-user"></a>C1 Felhasználó létrehozása/szerkesztése

Ez a kérelem egyszerűen megvalósítható a tárolóban lévő elemek létrehozásakor vagy frissítésekor `users` . A kérések szépen oszlanak meg az összes partíción a `id` partíciós kulcsnak köszönhetően.

:::image type="content" source="./media/how-to-model-partition-example/V1-C1.png" alt-text="Egyetlen elem írása a felhasználók tárolójába" border="false":::

| **Késés** | **RU díj** | **Teljesítmény** |
| --- | --- | --- |
| 7 MS | 5,71 RU | ✅ |

### <a name="q1-retrieve-a-user"></a>Első Felhasználó beolvasása

A felhasználók beolvasása a tároló megfelelő elemének beolvasásával történik `users` .

:::image type="content" source="./media/how-to-model-partition-example/V1-Q1.png" alt-text="Egyetlen elem beolvasása a felhasználók tárolójából" border="false":::

| **Késés** | **RU díj** | **Teljesítmény** |
| --- | --- | --- |
| 2 MS | 1 RU | ✅ |

### <a name="c2-createedit-a-post"></a>C2 Bejegyzés létrehozása/szerkesztése

A **[C1]**-hez hasonlóan csak írni kell a `posts` tárolóba.

:::image type="content" source="./media/how-to-model-partition-example/V1-C2.png" alt-text="Egyetlen elem írása a Posts tárolóba" border="false":::

| **Késés** | **RU díj** | **Teljesítmény** |
| --- | --- | --- |
| 9 MS | 8,76 RU | ✅ |

### <a name="q2-retrieve-a-post"></a>Q2 Bejegyzés beolvasása

Kezdjük azzal, hogy beolvassa a megfelelő dokumentumot a `posts` tárolóból. De ez nem elég, a specifikációnk alapján pedig összesíteni kell a bejegyzés szerzőjének felhasználónevét, valamint azt is, hogy hány Megjegyzés és hány hozzászólás van jelen a bejegyzésben, amelyhez 3 további SQL-lekérdezést kell kiállítani.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q2.png" alt-text="Bejegyzés beolvasása és további adatok összesítése" border="false":::

A további lekérdezési szűrők a megfelelő tárolójának partíciós kulcsán alapulnak, ami pontosan a teljesítmény és a méretezhetőség maximalizálását kívánja. Végül azonban négy műveletet kell végrehajtania egy adott bejegyzés visszaadásához, ezért a következő iteráció során javítunk.

| **Késés** | **RU díj** | **Teljesítmény** |
| --- | --- | --- |
| 9 MS | 19,54 RU | ⚠ |

### <a name="q3-list-a-users-posts-in-short-form"></a>Q3 A felhasználó bejegyzéseinek rövid formában való listázása

Először le kell kérnie a kívánt bejegyzéseket egy olyan SQL-lekérdezéssel, amely az adott felhasználóhoz tartozó bejegyzéseket kéri le. Emellett további lekérdezéseket is ki kell állítania a szerző felhasználónevének és a hozzászólások számának és a kívánt számoknak az összesítéséhez.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q3.png" alt-text="A felhasználók összes bejegyzésének beolvasása és a további adatok összesítése" border="false":::

Ez a megvalósítás számos hátrányat jelent:

- az első lekérdezés által visszaadott összes bejegyzés esetében a megjegyzések számának és az azt követő lekérdezéseknek a kiosztását kell kiállítani.
- a főlekérdezés nem szűri a tároló partíciós kulcsát `posts` , ami egy ventilátor-és egy partíciós vizsgálathoz vezet a tárolón keresztül.

| **Késés** | **RU díj** | **Teljesítmény** |
| --- | --- | --- |
| 130 MS | 619,41 RU | ⚠ |

### <a name="c3-create-a-comment"></a>C3 csomag Megjegyzés létrehozása

A Megjegyzés létrehozásához írja be a megfelelő elemeket a `posts` tárolóba.

:::image type="content" source="./media/how-to-model-partition-example/V1-C2.png" alt-text="Egyetlen elem írása a Posts tárolóba" border="false":::

| **Késés** | **RU díj** | **Teljesítmény** |
| --- | --- | --- |
| 7 MS | 8,57 RU | ✅ |

### <a name="q4-list-a-posts-comments"></a>Q4 Bejegyzés megjegyzésének listázása

Kezdjük egy olyan lekérdezéssel, amely beolvassa az adott bejegyzéshez tartozó összes hozzászólást, és még egyszer, a felhasználóneveket külön kell összesíteni az egyes megjegyzésekhez.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q4.png" alt-text="Bejegyzés összes megjegyzésének beolvasása és a további adatok összesítése" border="false":::

Bár a fő lekérdezés a tároló partíciós kulcsát szűri, a felhasználónevek összesítése külön bünteti a teljes teljesítményt. Ezt később fejlesztjük.

| **Késés** | **RU díj** | **Teljesítmény** |
| --- | --- | --- |
| 23 MS | 27,72 RU | ⚠ |

### <a name="c4-like-a-post"></a>C4 Mint egy post

A **[C3]**-hoz hasonlóan a megfelelő elem is létrejön a `posts` tárolóban.

:::image type="content" source="./media/how-to-model-partition-example/V1-C2.png" alt-text="Egyetlen elem írása a Posts tárolóba" border="false":::

| **Késés** | **RU díj** | **Teljesítmény** |
| --- | --- | --- |
| 6 MS | 7,05 RU | ✅ |

### <a name="q5-list-a-posts-likes"></a>Q5 A post kedvelt listájának listázása

Akárcsak a **[Q4]** esetében, lekérdezjük az adott bejegyzésre vonatkozó, majd összesített felhasználóneveket.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q5.png" alt-text="Az összes kedvelt üzenet beolvasása és a további adatok összesítése" border="false":::

| **Késés** | **RU díj** | **Teljesítmény** |
| --- | --- | --- |
| 59 MS | 58,92 RU | ⚠ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>Q6 A rövid formátumban létrehozott x legújabb bejegyzések listázása (hírcsatorna)

A legutóbbi hozzászólások beolvasásához a tároló lekérdezése `posts` csökkenő létrehozási dátummal történik, majd összesíti a felhasználóneveket és a hozzászólások számát, és szereti az egyes bejegyzéseket.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q6.png" alt-text="A Legutóbbi bejegyzések beolvasása és a további adatok összesítése" border="false":::

A kezdeti lekérdezés nem a tároló partíciós kulcsára van szűrve `posts` , ami költséges ventilátort indít el. Ez még rosszabb, mint a sokkal nagyobb eredményhalmaz megcélzása és az eredmények egy záradékkal való rendezése `ORDER BY` , ami drágább a kérési egységek tekintetében.

| **Késés** | **RU díj** | **Teljesítmény** |
| --- | --- | --- |
| 306 MS | 2063,54 RU | ⚠ |

## <a name="reflecting-on-the-performance-of-v1"></a>A v1 teljesítményének tükrözése

Tekintse át az előző szakaszban felmerülő teljesítménnyel kapcsolatos problémákat: a problémák két fő osztálya azonosítható:

- néhány kérelemhez több lekérdezésre van szükség a visszaadott adatok összegyűjtéséhez.
- egyes lekérdezések nem szűrik a célként megadott tárolók partíciós kulcsát, ami egy olyan ventilátort eredményez, amely akadályozza a méretezhetőséget.

Hárítsa el ezeket a problémákat, kezdve az elsővel.

## <a name="v2-introducing-denormalization-to-optimize-read-queries"></a>V2: a denormalizálás bevezetése az olvasási lekérdezések optimalizálására

Az OK, amiért bizonyos esetekben további kérelmeket kell kiadnia, mert a kezdeti kérés eredményei nem tartalmazzák az összes visszaadott adatmennyiséget. Ha olyan nem kapcsolattal rendelkező adattárakkal dolgozik, mint például a Azure Cosmos DB, az ilyen jellegű problémát általában az adathalmazon belüli adatnormalizálás okozta.

A példánkban módosítjuk a post elemek hozzáadását a bejegyzés szerzője, a Hozzászólások száma és a kedvelt szám:

```json
{
    "id": "<post-id>",
    "type": "post",
    "postId": "<post-id>",
    "userId": "<post-author-id>",
    "userUsername": "<post-author-username>",
    "title": "<post-title>",
    "content": "<post-content>",
    "commentCount": <count-of-comments>,
    "likeCount": <count-of-likes>,
    "creationDate": "<post-creation-date>"
}
```

A megjegyzéseket és a hasonló elemeket is módosítjuk, hogy hozzá lehessen adni az azokat létrehozó felhasználó felhasználónevét:

```json
{
    "id": "<comment-id>",
    "type": "comment",
    "postId": "<post-id>",
    "userId": "<comment-author-id>",
    "userUsername": "<comment-author-username>",
    "content": "<comment-content>",
    "creationDate": "<comment-creation-date>"
}

{
    "id": "<like-id>",
    "type": "like",
    "postId": "<post-id>",
    "userId": "<liker-id>",
    "userUsername": "<liker-username>",
    "creationDate": "<like-creation-date>"
}
```

### <a name="denormalizing-comment-and-like-counts"></a>A megjegyzések és a hasonló számok denormalizálása

Azt szeretnénk elérni, hogy minden alkalommal, amikor hozzáadunk egy hozzászólást vagy hasonlót, a megfelelő bejegyzésben is megnöveli a `commentCount` vagy a `likeCount` -t. Mivel a `posts` tároló particionálva van `postId` , az új elem (Megjegyzés vagy hasonló) és a hozzá tartozó post Sit ugyanabban a logikai partícióban található. Ennek eredményeképpen a művelet végrehajtásához [tárolt eljárást](stored-procedures-triggers-udfs.md) használhatunk.

Miután létrehoz egy megjegyzést (**[C3]**) ahelyett, hogy csak egy új elem hozzáadása a `posts` tárolóhoz, a következő tárolt eljárást hívjuk a tárolón:

```javascript
function createComment(postId, comment) {
  var collection = getContext().getCollection();

  collection.readDocument(
    `${collection.getAltLink()}/docs/${postId}`,
    function (err, post) {
      if (err) throw err;

      post.commentCount++;
      collection.replaceDocument(
        post._self,
        post,
        function (err) {
          if (err) throw err;

          comment.postId = postId;
          collection.createDocument(
            collection.getSelfLink(),
            comment
          );
        }
      );
    })
}
```

Ez a tárolt eljárás a bejegyzés és az új Megjegyzés törzsének AZONOSÍTÓját veszi fel paraméterekként, majd:

- a bejegyzés beolvasása
- növeli a `commentCount`
- a bejegyzés cseréje
- hozzáadja az új megjegyzést

Mivel a tárolt eljárások atomi tranzakcióként vannak végrehajtva, a `commentCount` és a hozzászólások tényleges száma mindig szinkronban marad.

Nyilvánvalóan hasonló tárolt eljárást hívjuk fel, amikor új, az érték növelését szereti `likeCount` .

### <a name="denormalizing-usernames"></a>Felhasználónevek denormalizálása

A felhasználónevek eltérő megközelítést igényelnek, mivel a felhasználók nem csak a különböző partíciókban ülnek, hanem egy másik tárolóban. Ha a partíciók és tárolók közötti adattitkosítást le kell állítani, a forrás tároló [változási csatornáját](change-feed.md)is használhatja.

A példánkban a tároló változási csatornáját használjuk arra, `users` hogy reagáljon arra, amikor a felhasználók frissítik a felhasználóneveket. Ebben az esetben a változást egy másik tárolt eljárás meghívásával propagáljuk a `posts` tárolón:

:::image type="content" source="./media/how-to-model-partition-example/denormalization-1.png" alt-text="Felhasználónevek denormalizálása a Posts tárolóba" border="false":::

```javascript
function updateUsernames(userId, username) {
  var collection = getContext().getCollection();
  
  collection.queryDocuments(
    collection.getSelfLink(),
    `SELECT * FROM p WHERE p.userId = '${userId}'`,
    function (err, results) {
      if (err) throw err;

      for (var i in results) {
        var doc = results[i];
        doc.userUsername = username;

        collection.upsertDocument(
          collection.getSelfLink(),
          doc);
      }
    });
}
```

Ez a tárolt eljárás a felhasználó AZONOSÍTÓját és a felhasználó új felhasználónevét adja meg paraméterként, majd:

- lekéri az összes elemet `userId` (amely lehet hozzászólások, hozzászólások vagy kedvelő)
- mindegyik elemhez
  - lecseréli a `userUsername`
  - az tétel cseréje

> [!IMPORTANT]
> Ez a művelet költséges, mert ez a tárolt eljárás végrehajtásához szükséges a tároló minden partícióján `posts` . Feltételezzük, hogy a legtöbb felhasználó megfelelő felhasználónevet választ a regisztráció során, és soha nem változtatja meg, így ez a frissítés nagyon ritkán fog futni.

## <a name="what-are-the-performance-gains-of-v2"></a>Mik a v2 teljesítménybeli nyereségek?

### <a name="q2-retrieve-a-post"></a>Q2 Bejegyzés beolvasása

Most, hogy a denormalizálás érvényben van, csak egyetlen elemből kell beolvasnia a kérés kezelését.

:::image type="content" source="./media/how-to-model-partition-example/V2-Q2.png" alt-text="Egyetlen elem beolvasása a Posts tárolóból" border="false":::

| **Késés** | **RU díj** | **Teljesítmény** |
| --- | --- | --- |
| 2 MS | 1 RU | ✅ |

### <a name="q4-list-a-posts-comments"></a>Q4 Bejegyzés megjegyzésének listázása

Itt is megteheti a felhasználónevek beolvasására szolgáló további kéréseket, és végül egyetlen lekérdezéssel, amely a partíciós kulcsra szűr.

:::image type="content" source="./media/how-to-model-partition-example/V2-Q4.png" alt-text="Bejegyzés összes megjegyzésének beolvasása" border="false":::

| **Késés** | **RU díj** | **Teljesítmény** |
| --- | --- | --- |
| 4 MS | 7,72 RU | ✅ |

### <a name="q5-list-a-posts-likes"></a>Q5 A post kedvelt listájának listázása

Ugyanezt a helyzetet kell megegyeznie, amikor felsorolja az szereti.

:::image type="content" source="./media/how-to-model-partition-example/V2-Q5.png" alt-text="Egy bejegyzéshez tartozó összes üzenet beolvasása" border="false":::

| **Késés** | **RU díj** | **Teljesítmény** |
| --- | --- | --- |
| 4 MS | 8,92 RU | ✅ |

## <a name="v3-making-sure-all-requests-are-scalable"></a>V3: az összes kérelem méretezhetőségének biztosítása

Az általános teljesítménybeli tökéletesítéseken még két kérelem is található, amelyeket nem teljesen optimalizáltunk: **[Q3]** és **[K6]**. Ezek olyan lekérdezések, amelyek nem szűrik az általuk megcélzott tárolók partíciós kulcsára vonatkozó kérelmeket.

### <a name="q3-list-a-users-posts-in-short-form"></a>Q3 A felhasználó bejegyzéseinek rövid formában való listázása

Ez a kérelem már a v2-ben bevezetett Újdonságok előnyeit is kihasználja, így további lekérdezéseket takaríthat meg.

:::image type="content" source="./media/how-to-model-partition-example/V2-Q3.png" alt-text="Diagram, amely megjeleníti a felhasználó bejegyzéseit rövid formában listázó lekérdezést." border="false":::

A fennmaradó lekérdezés azonban továbbra sem a tároló partíciós kulcsára van szűrve `posts` .

Ennek a helyzetnek a meggondolása valójában egyszerű:

1. A kérésnek szűrnie kell a *-t,* `userId` mert egy adott felhasználó összes bejegyzését szeretné beolvasni
1. Nem jól teljesíti, mert a `posts` tárolón fut, amelyet a nem particionál `userId`
1. Egyértelművé tettük a teljesítménnyel kapcsolatos problémát, ha a kérelmet egy olyan tárolón hajtja *végre, amelyet a következő particionál:*`userId`
1. Kiderül, hogy már van ilyen tárolónk: a `users` tároló!

Ezért bevezetjük a denormalizálás második szintjét úgy, hogy a teljes bejegyzést a `users` tárolóba duplikálja. Ennek során gyakorlatilag beolvasjuk a hozzászólásaink egy példányát, és csak egy másik dimenzió mentén particionáljuk, így hatékonyabban lehet lekérni őket `userId` .

A `users` tároló most két típusú elemet tartalmaz:

```json
{
    "id": "<user-id>",
    "type": "user",
    "userId": "<user-id>",
    "username": "<username>"
}

{
    "id": "<post-id>",
    "type": "post",
    "postId": "<post-id>",
    "userId": "<post-author-id>",
    "userUsername": "<post-author-username>",
    "title": "<post-title>",
    "content": "<post-content>",
    "commentCount": <count-of-comments>,
    "likeCount": <count-of-likes>,
    "creationDate": "<post-creation-date>"
}
```

Vegye figyelembe:

- bevezetünk egy `type` mezőt a felhasználói elemben, hogy megkülönböztessék a felhasználókat a bejegyzésektől,
- hozzáadunk egy mezőt is `userId` a felhasználói elemhez, amely redundáns a `id` mezővel, de szükség van rá, mivel a `users` tároló most már particionálva van `userId` (és nem `id` korábban)

A denormalizálás érdekében ismét a változási csatornát használjuk. Ezúttal a tároló változási csatornáján reagálunk arra, `posts` hogy új vagy frissített bejegyzést küldjön a `users` tárolóba. Mivel a bejegyzések bejegyzései nem igénylik a teljes tartalom visszaküldését, a folyamat során lerövidítheti azokat.

:::image type="content" source="./media/how-to-model-partition-example/denormalization-2.png" alt-text="Bejegyzések denormalizálása a felhasználók tárolójába" border="false":::

Most már elvégezhető a lekérdezés átirányítása a `users` tárolóba, a tároló partíciós kulcsának szűrésével.

:::image type="content" source="./media/how-to-model-partition-example/V3-Q3.png" alt-text="Felhasználó összes bejegyzésének beolvasása" border="false":::

| **Késés** | **RU díj** | **Teljesítmény** |
| --- | --- | --- |
| 4 MS | 6,46 RU | ✅ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>Q6 A rövid formátumban létrehozott x legújabb bejegyzések listázása (hírcsatorna)

Ehhez hasonló helyzetet kell megbirkóznia: még a v2-ben bevezetett denormalizálás után a további lekérdezések megkímélte után is, a fennmaradó lekérdezés nem szűri a tároló partíciós kulcsát:

:::image type="content" source="./media/how-to-model-partition-example/V2-Q6.png" alt-text="Azt a diagramot, amely a rövid formátumban létrehozott x legutóbbi álláshelyek listázására szolgáló lekérdezést mutatja." border="false":::

Ugyanezt a megközelítést követve a kérés teljesítményének és méretezhetőségének maximalizálása megköveteli, hogy csak egy partíciót lásson. Ez elképzelhető, hogy csak korlátozott számú elemet kell visszaadnia. a blogírás platform kezdőlapjának feltöltéséhez csupán a 100 legújabb bejegyzéseit kell megszereznie, anélkül, hogy a teljes adathalmazon át kellene lapozni.

A legutóbbi kérelem optimalizálása érdekében egy harmadik tárolót vezetünk be a kialakításba, amely kizárólag a kérelem kiszolgálására szolgál. A bejegyzéseket az új tárolóra denormalizáljuk `feed` :

```json
{
    "id": "<post-id>",
    "type": "post",
    "postId": "<post-id>",
    "userId": "<post-author-id>",
    "userUsername": "<post-author-username>",
    "title": "<post-title>",
    "content": "<post-content>",
    "commentCount": <count-of-comments>,
    "likeCount": <count-of-likes>,
    "creationDate": "<post-creation-date>"
}
```

Ez a tároló particionálva van `type` , amely mindig `post` az elemek között lesz. Ezzel biztosíthatja, hogy a tároló összes eleme ugyanabban a partícióban maradjon.

A denormalizálás eléréséhez csak be kell kapcsolnia a változási csatornát, amelyet korábban már bevezetett, hogy az új tárolóba küldjön a bejegyzéseket. Fontos szem előtt tartani, hogy meg kell győződni arról, hogy csak a 100 legutóbbi bejegyzéseit tároljuk. Ellenkező esetben előfordulhat, hogy a tároló tartalma meghaladja a partíciók maximális méretét. Ezt úgy teheti meg, hogy minden alkalommal meghívja a [triggert](stored-procedures-triggers-udfs.md#triggers) , amikor egy dokumentumot adnak hozzá a tárolóhoz:

:::image type="content" source="./media/how-to-model-partition-example/denormalization-3.png" alt-text="Bejegyzések denormalizálása a hírcsatorna-tárolóba" border="false":::

Itt látható a gyűjteményt lerövidítő trigger törzse:

```javascript
function truncateFeed() {
  const maxDocs = 100;
  var context = getContext();
  var collection = context.getCollection();

  collection.queryDocuments(
    collection.getSelfLink(),
    "SELECT VALUE COUNT(1) FROM f",
    function (err, results) {
      if (err) throw err;

      processCountResults(results);
    });

  function processCountResults(results) {
    // + 1 because the query didn't count the newly inserted doc
    if ((results[0] + 1) > maxDocs) {
      var docsToRemove = results[0] + 1 - maxDocs;
      collection.queryDocuments(
        collection.getSelfLink(),
        `SELECT TOP ${docsToRemove} * FROM f ORDER BY f.creationDate`,
        function (err, results) {
          if (err) throw err;

          processDocsToRemove(results, 0);
        });
    }
  }

  function processDocsToRemove(results, index) {
    var doc = results[index];
    if (doc) {
      collection.deleteDocument(
        doc._self,
        function (err) {
          if (err) throw err;

          processDocsToRemove(results, index + 1);
        });
    }
  }
}
```

Az utolsó lépés a lekérdezés átirányítása az új `feed` tárolóra:

:::image type="content" source="./media/how-to-model-partition-example/V3-Q6.png" alt-text="Legutóbbi bejegyzések beolvasása" border="false":::

| **Késés** | **RU díj** | **Teljesítmény** |
| --- | --- | --- |
| 9 MS | 16,97 RU | ✅ |

## <a name="conclusion"></a>Összegzés

Tekintse át a tervünk különböző verzióiban bevezetett általános teljesítmény-és méretezhetőségi funkciókat.

| | 1. verzió | 2. verzió | V3 |
| --- | --- | --- | --- |
| **C1** | 7 MS/5,71 RU | 7 MS/5,71 RU | 7 MS/5,71 RU |
| **Első** | 2 MS/1 RU | 2 MS/1 RU | 2 MS/1 RU |
| **C2** | 9 MS/8,76 RU | 9 MS/8,76 RU | 9 MS/8,76 RU |
| **Q2** | 9 MS/19,54 RU | 2 MS/1 RU | 2 MS/1 RU |
| **Q3** | 130 MS/619,41 RU | 28 MS/201,54 RU | 4 MS/6,46 RU |
| **C3 csomag** | 7 MS/8,57 RU | 7 MS/15,27 RU | 7 MS/15,27 RU |
| **Q4** | 23 MS/27,72 RU | 4 MS/7,72 RU | 4 MS/7,72 RU |
| **C4** | 6 MS/7,05 RU | 7 MS/14,67 RU | 7 MS/14,67 RU |
| **Q5** | 59 MS/58,92 RU | 4 MS/8,92 RU | 4 MS/8,92 RU |
| **Q6** | 306 MS/2063,54 RU | 83 MS/532,33 RU | 9 MS/16,97 RU |

### <a name="we-have-optimized-a-read-heavy-scenario"></a>Optimalizáltunk egy nagy olvasási forgatókönyvet

Előfordulhat, hogy észrevette, hogy az írási kérések (parancsok) rovására az olvasási kérelmek (lekérdezések) teljesítményének javítására tettük erőfeszítéseit. Sok esetben az írási műveletek mostantól a változási csatornákon keresztül is kiváltják a későbbi denormalizálás folyamatát, ami számítási feladatokhoz és a hosszabb számításokhoz is szükséges.

Ezt az a tény indokolja, hogy egy blogging platform (például a legtöbb közösségi alkalmazás) olvasási nehéz, ami azt jelenti, hogy a kiszolgálni kívánt olvasási kérelmek mennyisége általában nagyobb, mint az írási kérelmek mennyisége. Ezért érdemes az írási kérelmeket a végrehajtáshoz drágábban végrehajtani ahhoz, hogy az olvasási kérések olcsóbbak és hatékonyabbak legyenek.

Ha megvizsgáljuk a legszélsőségesebb optimalizálást, **[K6]** 2000 + RUs-ról csak 17 RUs-ra került sor. elértük, hogy a bejegyzések denormalizálása körülbelül 10 RUs/tétel áron történik. Mivel sokkal több adatcsatorna-kérést fogunk kiszolgálni, mint a bejegyzések létrehozása vagy frissítése, a denormalizálás díja elhanyagolható a teljes megtakarítás miatt.

### <a name="denormalization-can-be-applied-incrementally"></a>A denormalizálás növekményes módon alkalmazható

A jelen cikkben ismertetett skálázhatósági funkciók az adathalmazon belüli adatnormalizálás és-ismétlődések bevonásával járnak. Fontos megjegyezni, hogy ezek az optimalizálások nem szükségesek az 1. napon való üzembe helyezéshez. A partíciós kulcsokra szűrt lekérdezések nagy méretekben működnek, a több partíciós lekérdezések azonban teljesen elfogadhatók lehetnek, ha ritkán vagy korlátozott adathalmazon vannak elnevezve. Ha csak prototípust készít, vagy egy terméket kis és ellenőrzött felhasználói alappal indít el, akkor valószínűleg később is megkímélheti ezeket a fejlesztéseket; Mi a fontos, hogy [Figyelje](use-metrics.md) a modell teljesítményét, így eldöntheti, hogy mikor és mikor kell azokat bevezetni.

Az a változás, amelyet a frissítések más tárolók számára történő terjesztésére használunk, az összes frissítést tartósan tárolja. Ez lehetővé teszi az összes frissítés igénylését, mivel a tároló létrehozása és a rendszerindítási műveletek elvégezte a betöltési nézetek egyszeri észlelési műveletként, még akkor is, ha a rendszer már sok adattal rendelkezik.

## <a name="next-steps"></a>Következő lépések

A gyakorlati adatmodellezés és particionálás után érdemes megtekinteni a következő cikkeket az érintett fogalmak áttekintéséhez:

- [Adatbázisok, tárolók és elemek használata](account-databases-containers-items.md)
- [Particionálás az Azure Cosmos DB-ben](partitioning-overview.md)
- [Adatcsatorna módosítása Azure Cosmos DB](change-feed.md)

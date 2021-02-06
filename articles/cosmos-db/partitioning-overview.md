---
title: Particionálás és horizontális skálázás az Azure Cosmos DB-ben
description: Ismerje meg a particionálást, a logikai és fizikai partíciókat Azure Cosmos DBban, ajánlott eljárásokat a partíciós kulcs kiválasztásakor, valamint a logikai partíciók kezelését.
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/12/2020
ms.openlocfilehash: b91c846b5a79125c1cee9c36ce81b5c3d3229ba9
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627770"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Particionálás és horizontális skálázás az Azure Cosmos DB-ben
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

A Azure Cosmos DB particionálás használatával méretezi az egyes tárolókat egy adatbázisban az alkalmazás teljesítménybeli igényeinek kielégítése érdekében. A particionálás során a tároló elemei a *logikai partíciók* nevű különálló részhalmazokra vannak osztva. A logikai partíciók a tároló egyes elemeihez társított *partíciós kulcs* értéke alapján jönnek létre. Egy logikai partíció összes elemének ugyanaz a partíciós kulcs értéke.

Egy tároló például elemeket tárol. Minden egyes tétel egyedi értékkel rendelkezik a `UserID` tulajdonsághoz. Ha a `UserID` tárolóban lévő elemek partíciós kulcsaként szolgál, és 1 000 egyedi érték van `UserID` , akkor a rendszer 1 000 logikai partíciókat hoz létre a tárolóhoz.

Egy olyan partíciós kulcs mellett, amely meghatározza az elem logikai partícióját, a tároló minden eleméhez tartozik egy *elem azonosítója* (egyedi logikai partíción belül). A partíciós kulcs és az *elem azonosítójának* kombinálásával létrejön az elem *indexe*, amely egyedileg azonosítja az adott tételt. [A partíciós kulcs kiválasztása](#choose-partitionkey) fontos döntés, amely hatással lesz az alkalmazás teljesítményére.

Ez a cikk a logikai és fizikai partíciók közötti kapcsolatot ismerteti. Emellett a particionálással kapcsolatos ajánlott eljárásokat is ismerteti, és részletesen ismerteti, hogyan működik a horizontális skálázás a Azure Cosmos DBban. Ezen belső adatok megértéséhez nem szükséges, hogy kiválassza a partíciós kulcsot, de a kezeltük, hogy egyértelmű legyen a Azure Cosmos DB működése.

## <a name="logical-partitions"></a>Logikai partíciók

A logikai partíciók olyan elemekből állnak, amelyek ugyanazzal a partíciós kulccsal rendelkeznek. Például egy olyan tárolóban, amely az élelmiszer-táplálkozással kapcsolatos adatokat tartalmaz, minden elem tartalmaz egy `foodGroup` tulajdonságot. A `foodGroup` tároló partíciós kulcsaként is használható. Olyan elemek csoportjai, amelyek meghatározott értékekkel rendelkeznek `foodGroup` , például,, `Beef Products` `Baked Products` és `Sausages and Luncheon Meats` , különböző logikai partíciókat alkotnak. Nem kell aggódnia a logikai partíció törlésével kapcsolatban az alapul szolgáló adat törlésekor.

A logikai partíció az adatbázis-tranzakciók hatókörét is meghatározza. A logikai partíción belüli elemeket egy [Pillanatkép-elkülönítéssel rendelkező tranzakció](database-transactions-optimistic-concurrency.md)használatával frissítheti. Amikor új elemeket adnak hozzá egy tárolóhoz, a rendszer transzparens módon létrehozza az új logikai partíciókat.

A tárolóban található logikai partíciók száma nincs korlátozva. Minden logikai partíció akár 20 GB-ot is tárolhat. A jó partíciós kulcs választása a lehetséges értékek széles választékával rendelkezik. Például egy olyan tárolóban, ahol minden elem tartalmaz egy `foodGroup` tulajdonságot, a `Beef Products` logikai partíción belüli adatok 20 GB-ig növekednek. A lehetséges értékek széles választékával rendelkező [partíciós kulcs kiválasztásával](#choose-partitionkey) biztosítható, hogy a tároló méretezhető legyen.

## <a name="physical-partitions"></a>Fizikai partíciók

A tárolók méretezése az adatok és az átviteli sebesség elosztásával történik a fizikai partíciók között. Belsőleg egy vagy több logikai partíció egyetlen fizikai partícióra van leképezve. Általában a kisebb tárolók számos logikai partícióval rendelkeznek, de csak egyetlen fizikai partíciót igényelnek. A logikai partíciókkal ellentétben a fizikai partíciók a rendszer belső implementációja, és teljes mértékben a Azure Cosmos DB kezeli őket.

A tárolóban lévő fizikai partíciók száma a következőktől függ:

* A kiépített átviteli sebesség száma (az egyes fizikai partíciók másodpercenként legfeljebb 10 000 adatátviteli sebességet biztosíthatnak).
* A teljes adattárolás (minden egyes fizikai partíció akár 50 GB-adatmennyiséget is tárolhat).

> [!NOTE]
> A fizikai partíciók a rendszer belső megvalósítása, és teljes mértékben a Azure Cosmos DB felügyelik. A megoldások fejlesztésekor ne koncentráljon a fizikai partícióra, mert nem tudja szabályozni őket. Ehelyett a partíciós kulcsokra kell összpontosítania. Ha olyan partíciós kulcsot választ, amely egyenletesen osztja el az átviteli sebességet a logikai partíciók között, akkor gondoskodni fog arról, hogy a fizikai partíciók átviteli sebessége egyensúlyban legyen.

A tárolóban lévő fizikai partíciók teljes száma nincs korlátozva. A kiosztott átviteli sebesség vagy az adatméret növekedésével a Azure Cosmos DB automatikusan új fizikai partíciókat hoz létre a meglévők felosztásával. A fizikai partíciók osztása nem befolyásolja az alkalmazás rendelkezésre állását. A fizikai partíció felosztása után az egyetlen logikai partíción belüli összes adattal továbbra is ugyanazon a fizikai partíción lesz tárolva. A fizikai partíciók megosztása egyszerűen létrehozza a logikai partíciók új hozzárendelését a fizikai partíciókhoz.

A tárolók számára kiépített átviteli sebesség egyenletesen oszlik el a fizikai partíciók között. A partíciós kulcs olyan kialakítása, amely nem terjeszti a kérelmeket, túl sok kérést eredményez a partíciók kis részhalmaza számára, amely "gyors" lesz. A gyakori partíciók a kiépített átviteli sebesség nem hatékony kihasználásához vezetnek, ami a ráta korlátozásával és a magasabb költségekkel járhat.

A tároló fizikai partícióit a Azure Portal **metrika** panelének **Storage (tárolás** ) szakaszában tekintheti meg:

:::image type="content" source="./media/partitioning-overview/view-partitions-zoomed-out.png" alt-text="Fizikai partíciók számának megtekintése" lightbox="./media/partitioning-overview/view-partitions-zoomed-in.png" ::: 

A fenti képernyőképen egy tároló `/foodGroup` a partíciós kulcs. A gráf mindhárom bárja egy fizikai partíciót jelöl. A rendszerképben a **partíciós kulcs tartománya** ugyanaz, mint a fizikai partíció. A kiválasztott fizikai partíció a legfelső 3 legjelentősebb logikai partíciót tartalmazza: `Beef Products` , `Vegetable and Vegetable Products` és `Soups, Sauces, and Gravies` .

Ha a másodpercenkénti 18 000-kérelmeket (RU/s) használja, akkor a három fizikai partíció esetében a teljes kiépített átviteli sebesség 1/3. A kiválasztott fizikai partíción belül a logikai partíció kulcsai `Beef Products` , `Vegetable and Vegetable Products` és `Soups, Sauces, and Gravies` együttesen a fizikai partíció 6 000 kiépített ru/s-t használhatják. Mivel a kiépített átviteli sebesség egyenletesen oszlik meg a tároló fizikai partíciói között, fontos, hogy olyan partíciós kulcsot válasszon, amely egyenletesen osztja el az átviteli sebességet [a megfelelő logikai partíciós kulcs kiválasztásával](#choose-partitionkey). 

## <a name="managing-logical-partitions"></a>Logikai partíciók kezelése

Azure Cosmos DB átlátható módon, és automatikusan kezeli a logikai partíciók elhelyezését a fizikai partíciókon, hogy hatékonyan kielégítse a tároló méretezhetőségét és teljesítményét. Az alkalmazások adatátviteli és tárolási követelményeinek növekedésével Azure Cosmos DB a logikai partíciók mozgatásával automatikusan elosztja a terhelést a több fizikai partíció között. További információ a [fizikai partíciók](partitioning-overview.md#physical-partitions)szolgáltatásról.

A Azure Cosmos DB kivonatoló particionálást használ a logikai partíciók fizikai partíciók közötti elterjesztéséhez. Azure Cosmos DB kivonatok egy adott tétel partíciós kulcsának értékét. A kivonatos eredmény határozza meg a fizikai partíciót. Ezután a Azure Cosmos DB a partíciós kulcsok kivonatait egyenletesen osztja el a fizikai partíciók között.

A tranzakciókat (tárolt eljárásokban vagy eseményindítókban) csak egyetlen logikai partíció elemeire lehet engedélyezni.

További tudnivalókat a [Azure Cosmos db a partíciók kezelése](partitioning-overview.md)című témakörben olvashat. (Nem szükséges megérteni az alkalmazások létrehozásához és futtatásához szükséges belső adatokat, de itt egy kíváncsi olvasóhoz is hozzá lehet adni őket.)

## <a name="replica-sets"></a>Replikakészletek

Mindegyik fizikai partíció replikák halmazát, más néven [*replikakészlet*](global-dist-under-the-hood.md)-készletet tartalmaz. Mindegyik replikakészlet az adatbázismotor egy példányát tárolja. A replikakészlet a fizikai partícióban tárolt, tartós, magasan elérhető és konzisztens módon tárolja az adattárolási készletet. A fizikai partíciót alkotó replikák öröklik a partíció tárolási kvótáját. A fizikai partíció összes replikája együttesen támogatja a fizikai partícióhoz lefoglalt átviteli sebességet. A Azure Cosmos DB automatikusan kezeli a replikákat.

A kisebb tárolók jellemzően csak egyetlen fizikai partíciót igényelnek, de továbbra is legalább 4 replikával rendelkeznek.

Az alábbi képen látható, hogyan vannak leképezve a logikai partíciók a globálisan elosztott fizikai partíciókhoz:

:::image type="content" source="./media/partitioning-overview/logical-partitions.png" alt-text="Azure Cosmos DB particionálást bemutató rendszerkép" border="false":::

## <a name="choosing-a-partition-key"></a><a id="choose-partitionkey"></a>Partíciókulcs kiválasztása

A partíciós kulcsnak két összetevője van: a **partíciós kulcs elérési útja** és a **partíciós kulcs értéke**. Vegyünk például egy {"userId": "Andrew", "worksFor": "Microsoft"} elemet, ha a "felhasználóazonosító" a partíciós kulcsként van kiválasztva, a következő két partíciós kulcs összetevő:

* A partíciós kulcs elérési útja (például: "/userId"). A partíciós kulcs elérési útja alfanumerikus és aláhúzás (_) karaktereket fogad el. A beágyazott objektumokat a szabványos elérési út (/) használatával is használhatja.

* A partíciós kulcs értéke (például: "Andrew"). A partíciós kulcs értéke lehet karakterlánc vagy numerikus típus.

Az átviteli sebességre, a tárterületre és a partíciós kulcs hosszára vonatkozó korlátokkal kapcsolatos további tudnivalókért tekintse meg a [Azure Cosmos db szolgáltatási kvótákat](concepts-limits.md) ismertető cikket.

A partíciós kulcs kiválasztásával egy egyszerű, de fontos kialakítási lehetőség van a Azure Cosmos DB. A partíciós kulcs kiválasztását követően nem lehet helyben módosítani. Ha módosítania kell a partíciós kulcsot, helyezze át az adatait egy új tárolóba az új kívánt partíciós kulccsal.

Az **összes** tároló esetében a partíciós kulcsnak a következőket kell tennie:

* Olyan tulajdonságnak kell lennie, amely nem módosítható értékkel rendelkezik. Ha a tulajdonság a partíciós kulcs, a tulajdonság értéke nem frissíthető.

* Magas számossággal rendelkezik. Más szóval a tulajdonságnak a lehetséges értékek széles körének kell lennie.

* A kérési egység (RU) felhasználásának és az adattárolásnak egyenletes elosztása az összes logikai partíción keresztül. Ez biztosítja a fizikai partíciók esetében is az RU-felhasználást és a tárolók eloszlását.

Ha Azure Cosmos DB [több tételes savas tranzakcióra](database-transactions-optimistic-concurrency.md#multi-item-transactions) van szüksége, akkor [tárolt eljárásokat vagy eseményindítókat](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures)kell használnia. Minden JavaScript-alapú tárolt eljárás és eseményindító hatóköre egyetlen logikai partícióra van korlátozva.

## <a name="partition-keys-for-read-heavy-containers"></a>Partíciós kulcsok olvasási-nehéz tárolók számára

A legtöbb tároló esetében a fenti feltételek csak a partíciós kulcsok kiválogatásakor szükségesek. Nagyméretű, nehezen olvasható tárolók esetében azonban érdemes lehet olyan partíciós kulcsot választania, amely gyakran jelenik meg szűrőként a lekérdezésekben. A lekérdezések [hatékonyan irányíthatók úgy, hogy csak a megfelelő fizikai partíciók számára legyenek átirányítva](how-to-query-container.md#in-partition-query) , a Filter predikátumban található partíciós kulccsal együtt.

Ha a számítási feladatok nagy része lekérdezésekből áll, és a legtöbb lekérdezés egy egyenlőségi szűrővel rendelkezik ugyanazon a tulajdonságon, akkor ez a tulajdonság lehet jó partíciós kulcs választása. Ha például gyakran futtat egy olyan lekérdezést, amely a szűrők alapján fut `UserID` , akkor `UserID` a partíciós kulcs kiválasztásával csökkentheti a több [partíciós lekérdezések](how-to-query-container.md#avoiding-cross-partition-queries)számát.

Ha azonban a tároló kicsi, valószínűleg nem rendelkezik elegendő fizikai partícióval, hogy ne kelljen aggódnia a több partíciós lekérdezések teljesítményére gyakorolt hatásával kapcsolatban. Azure Cosmos DB a legtöbb kisméretű tárolóhoz csak egy vagy két fizikai partíció szükséges.

Ha a tároló több fizikai partícióra is nőhet, akkor győződjön meg arról, hogy olyan partíciós kulcsot választ, amely a több partíciós lekérdezések minimalizálását végzi. A tároló több fizikai partíciót is igényel, ha az alábbiak egyike igaz:

* A tároló több mint 30 000 RU kiépítve lesz

* A tároló 100 GB-nál több adat tárolására is alkalmas

## <a name="using-item-id-as-the-partition-key"></a>Az Item ID használata partíciós kulcsként

Ha a tároló olyan tulajdonsággal rendelkezik, amely a lehetséges értékek széles választékával rendelkezik, valószínűleg nagyszerű partíciós kulcsra van kiválasztva. Ilyen tulajdonság egy lehetséges példa az *Item azonosító*. A kis olvasási nehéz tárolók vagy a nagy méretű írható tárolók esetében az *elem azonosítója* természetesen remek választás a partíciós kulcshoz.

A rendszertulajdonság- *azonosító* a tároló minden eleme esetében létezik. Lehet, hogy más tulajdonságokkal rendelkezik, amelyek az adott eleme logikai AZONOSÍTÓját jelölik. Sok esetben a partíciós kulcsra vonatkozó döntések is megegyeznek az *elem azonosítójának* indokairól.

Az *azonosító* egy nagyszerű partíciós kulcs, amely a következő okok miatt választható:

* A lehetséges értékek széles választéka (egy egyedi *azonosító* /tétel).
* Mivel egy elemhez egyedi *elem-azonosító* van, az *elem azonosítója* nagyszerű feladatot jelent az ru-használat és az adattárolás egyenletes elosztásával.
* Egyszerűen elvégezheti a hatékony pontok olvasását, mivel a rendszer mindig ismeri az elemek partíciós kulcsát, ha ismeri annak *azonosítóját*.

Az *elemek azonosítójának* kiválasztásakor figyelembe kell venni az alábbiakat:

* Ha az *elem azonosítója* a partíciós kulcs, akkor a teljes tárolóban egyedi azonosító lesz. Nem lesz lehetősége ismétlődő *elem-azonosítóval* rendelkező elemekre.
* Ha olyan nagy mennyiségű [fizikai partícióval](partitioning-overview.md#physical-partitions)rendelkező olvasási nehéz tárolóval rendelkezik, a lekérdezések hatékonyabbak lesznek, ha az *elem azonosítójának* megfelelő egyenlőségi szűrővel rendelkeznek.
* Nem futtathat tárolt eljárásokat vagy eseményindítókat több logikai partíción keresztül.

## <a name="next-steps"></a>Következő lépések

* További információ [a kiépített átviteli sebességről Azure Cosmos db](request-units.md).
* Ismerkedjen meg a [Azure Cosmos db globális eloszlásával](distribute-data-globally.md).
* Útmutató az [átviteli sebesség Azure Cosmos-tárolón](how-to-provision-container-throughput.md)való kiépítéséhez.
* Útmutató az [átviteli sebesség Azure Cosmos-adatbázison](how-to-provision-database-throughput.md)való kiépítéséhez.

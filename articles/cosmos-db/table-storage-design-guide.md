---
title: A méretezéshez és a teljesítményhez Azure Cosmos DB táblázatok tervezése
description: 'Az Azure Table Storage tervezési útmutatója: skálázható és teljesített táblázatok a Azure Cosmos DB és az Azure Table Storage szolgáltatásban'
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 06/19/2020
author: sakash279
ms.author: akshanka
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 603c891e53e5712d489fcef8415e3db55328c9ad
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988425"
---
# <a name="azure-table-storage-table-design-guide-scalable-and-performant-tables"></a>Az Azure Table Storage táblatervezési útmutatója: Skálázható és hatékony táblák
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

A méretezhető és nagy teljesítményű táblák tervezésénél rengeteg szempontot, köztük a költséget is figyelembe kell vennie. Ha korábban már tervezett sémákat relációs adatbázisokhoz, ismerősnek fogja találni ezeket a szempontokat. Bár az Azure Table Storage és a relációs modellek között vannak hasonlóságok, több fontos dologban is eltérnek egymástól. Ezek a különbségek általában olyan eltérő kialakításokhoz vezetnek, amelyek a relációs adatbázisokat ismerő személyek számára ellentmondásosnak vagy helytelennek tűnhetnek, azonban a NoSQL-alapú kulcs/érték tárolók, például a Table Storage számára való tervezésekor van értelmük.

A Table Storage úgy van kialakítva, hogy támogassa a felhőalapú alkalmazásokat, amelyek több milliárd entitást ("sorok") tartalmazhatnak a kapcsolódó adatbázis-terminológiában, illetve olyan adatkészleteket, amelyeknek támogatniuk kell a nagy tranzakciós köteteket. Ezért másképp kell gondolkodnia az adatai tárolásával kapcsolatban, és megismerheti a Table Storage működését. A jól megtervezett NoSQL-adattárak lehetővé teszik, hogy a megoldás sokkal tovább méretezhető legyen (és alacsonyabb költségeket is), mint a viszonyítási adatbázist használó megoldás. Ez az útmutató segítséget nyújt ezekről a témakörökről.  

## <a name="about-azure-table-storage"></a>Tudnivalók az Azure Table Storage szolgáltatásról
Ez a szakasz a Table Storage egyes főbb funkcióit mutatja be, amelyek különösen a teljesítmény és a méretezhetőség tervezéséhez szükségesek. Ha még nem ismeri az Azure Storage-t és a Table Storage-t, tekintse meg a jelen cikk további részének beolvasása című témakör [Microsoft Azure Storage](../storage/common/storage-introduction.md) és az [Azure Table Storage használatának első lépéseit](./tutorial-develop-table-dotnet.md) ismertető cikket. Bár ez az útmutató a Table Storage-ra koncentrál, az Azure üzenetsor-tárolással és az Azure Blob Storage-mel kapcsolatos néhány vitát tartalmaz, valamint azt, hogy miként használhatja őket a megoldásban a Table Storage szolgáltatással együtt.  

A Table Storage táblázatos formátumban tárolja az adatokat. A standard terminológiában a tábla minden sora egy entitást jelöl, és az oszlopok az entitás különböző tulajdonságait tárolják. Minden entitás rendelkezik egy pár kulccsal az egyedi azonosításhoz, valamint egy időbélyeg-oszlophoz, amelyet a Table Storage használ az entitás utolsó frissítésének nyomon követésére. Az időbélyeg mező automatikusan hozzáadódik, és nem lehet manuálisan felülírni az időbélyeget tetszőleges értékkel. A Table Storage ezt a legutóbb módosított időbélyeget (LMT) használja az optimista Egyidejűség kezelésére.  

> [!NOTE]
> A Table Storage REST API műveletei az `ETag` LMT-ból származtatott értéket is visszaadnak. Ebben a dokumentumban a ETag és az LMT kifejezéseket szinonimaként használjuk, mert ugyanazok az alapul szolgáló adatokat használják.  
> 
> 

Az alábbi példa egy egyszerű tábla-kialakítást mutat be az alkalmazottak és részleg entitások tárolásához. Az útmutatóban később bemutatott példák közül sokat ezen az egyszerű kialakításon alapul.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td>Marketing</td>
<td>00001</td>
<td>2014-08-22T00:50:32Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Életkor</th>
<th>E-mail</th>
</tr>
<tr>
<td>Don</td>
<td>Hall</td>
<td>34</td>
<td>donh@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>00002</td>
<td>2014-08-22T00:50:34Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Életkor</th>
<th>E-mail</th>
</tr>
<tr>
<td>Jún</td>
<td>Cao</td>
<td>47</td>
<td>junc@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>Részleg</td>
<td>2014-08-22T00:50:30Z</td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Marketing</td>
<td>153</td>
</tr>
</table>
</td>
</tr>
<tr>
<td>Sales</td>
<td>00010</td>
<td>2014-08-22T00:50:44Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Életkor</th>
<th>E-mail</th>
</tr>
<tr>
<td>Ken</td>
<td>Kwok</td>
<td>23</td>
<td>kenk@contoso.com</td>
</tr>
</table>
</td>
</tr>
</table>


Eddig ez a kialakítás a kapcsolódó adatbázisban lévő táblákhoz hasonlóan néz ki. A legfontosabb különbségek a kötelező oszlopok, és több entitás típusának lehetősége ugyanabban a táblában. Emellett a felhasználó által definiált tulajdonságok (például a **FirstName** vagy a **Age**) adattípusa (például Integer vagy string) egy, a rokon adatbázisban lévő oszlophoz hasonlóan. A relációs adatbázistól eltérően azonban a Table Storage séma nélküli jellege azt jelenti, hogy egy tulajdonságnak nem kell ugyanazzal az adattípussal rendelkeznie az egyes entitásokon. Az összetett adattípusok egyetlen tulajdonságba való tárolásához szerializált formátumot kell használni, például JSON vagy XML. További információ: a [Table Storage adatmodell ismertetése](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model).

Ön dönti el, `PartitionKey` és `RowKey` alapvető fontosságú a jó tábla kialakításához. A táblában tárolt összes entitásnak a és a egyedi kombinációjával kell `PartitionKey` rendelkeznie `RowKey` . A kapcsolati adatbázistábla kulcsaihoz hasonlóan a (z) `PartitionKey` és az `RowKey` értékek indexelve lettek egy fürtözött index létrehozásához, amely lehetővé teszi a gyors kereséseket. A Table Storage azonban nem hoz létre másodlagos indexeket, így ezek az egyetlen két indexelt tulajdonság (a később bemutatott minták némelyike azt mutatja be, hogyan lehet megkerülni a látszólagos korlátozást).  

Egy tábla egy vagy több partícióból áll, és számos tervezési döntés a megfelelő `PartitionKey` és a `RowKey` megoldás optimalizálására szolgál. A megoldások csak egyetlen táblából állhatnak, amely tartalmazza az összes olyan entitást, amely partícióra van osztva, de általában egy megoldás több táblával is rendelkezik. A táblázatok segítségével logikailag rendszerezheti az entitásokat, és a hozzáférés-vezérlési listák segítségével kezelheti az adathozzáférést. A teljes táblázatot egyetlen tárolási művelettel lehet eldobni.  

### <a name="table-partitions"></a>Tábla partíciói
A fiók neve, a tábla neve és `PartitionKey` együttesen a Storage szolgáltatásban található partíció, ahol a Table Storage tárolja az entitást. Emellett az entitások címzési sémájának részeként a partíciók határozzák meg a tranzakciók hatókörét (lásd a cikk későbbi részében, az [Entity Transactions-tranzakciók](#entity-group-transactions)című szakaszt), és a tábla tárolási skálázásának alapjait alkotják. További információ a Table Partitions szolgáltatásról: a [Table Storage teljesítmény-és méretezhetőségi ellenőrzőlistája](../storage/tables/storage-performance-checklist.md).  

A Table Storage szolgáltatásban az egyes csomópontok egy vagy több teljes partíciót, a szolgáltatás pedig a csomópontok közötti dinamikusan terheléselosztást végez. Ha egy csomópont terhelés alatt van, a Table Storage feloszthatja az adott csomópont által kiszolgált partíciók tartományát különböző csomópontokra. Ha a forgalom alá esik, a Table Storage képes egyesíteni a partíciók tartományait a csendes csomópontokból egyetlen csomópontra.  

További információ a Table Storage belső részleteiről és különösen a partíciók kezeléséről: [Microsoft Azure Storage: magas rendelkezésre állású felhőalapú tárolási szolgáltatás erős konzisztencia](/archive/blogs/windowsazurestorage/sosp-paper-windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency)mellett.  

### <a name="entity-group-transactions"></a>Entitás-csoport tranzakciói
A Table Storage szolgáltatásban az Entity Transactions (EGTs) az egyetlen beépített mechanizmus, amellyel több entitáson végezheti el az Atomic-frissítéseket. A EGTs más néven batch- *tranzakciónak* is nevezzük. A EGTs csak ugyanabban a partícióban tárolt entitásokban működhet (egy adott tábla azonos partíciójának megosztásával), így bármikor szükség van az atomi tranzakciós viselkedésre több entitás között, hogy az entitások ugyanabban a partícióban legyenek. Ez gyakran indokolja, hogy több entitást is tartson ugyanabban a táblában (és partícióban), és ne használjon több táblát a különböző típusú entitásokhoz. Egyetlen EGT legfeljebb 100 entitáson működhet.  Ha több párhuzamos EGTs küld a feldolgozáshoz, fontos, hogy ezek a EGTs ne működjenek olyan entitásokon, amelyek a EGTs-ben közösek. Ellenkező esetben a feldolgozás késleltetését kockáztatja.

A EGTs emellett potenciális kompromisszumot is bevezet, hogy kiértékelje a kialakítását. A több partíció használata növeli az alkalmazás méretezhetőségét, mivel az Azure több lehetőséget kínál a terheléselosztási kérelmek közötti terheléselosztásra. Ez azonban korlátozhatja, hogy az alkalmazás képes legyen atomi tranzakciókat végrehajtani, és erős konzisztenciát fenntartani az adatokhoz. Emellett vannak olyan méretezhetőségi célok is a partíció szintjén, amelyek korlátozhatják az egyetlen csomópontra várható tranzakciók átviteli sebességét.

Az Azure Storage-fiókok méretezhetőségi céljaival kapcsolatos további információkért lásd [a standard szintű Storage-fiókok méretezhetőségi céljait](../storage/common/scalability-targets-standard-account.md)ismertető témakört. A Table Storage skálázhatósági céljaival kapcsolatos további információkért lásd: [a táblázatos tárolás skálázhatósági és teljesítményi céljai](../storage/tables/scalability-targets.md). A jelen útmutató későbbi szakaszai különböző tervezési stratégiákat tárgyalnak, amelyek segítségével kezelheti az ilyen jellegű kompromisszumokat, és megtudhatja, hogyan választhatja ki legjobban a partíciós kulcsot az ügyfélalkalmazás konkrét követelményei alapján.  

### <a name="capacity-considerations"></a>Kapacitással kapcsolatos szempontok
A következő táblázat néhány kulcsfontosságú értéket tartalmaz, amelyek a Table Storage-megoldás tervezésekor szükségesek:  

| Azure Storage-fiók teljes kapacitása | 500 TB |
| --- | --- |
| Táblák száma egy Azure Storage-fiókban |Csak a Storage-fiók kapacitása korlátozza. |
| Egy tábla partícióinak száma |Csak a Storage-fiók kapacitása korlátozza. |
| Egy partícióban lévő entitások száma |Csak a Storage-fiók kapacitása korlátozza. |
| Egyéni entitás mérete |Legfeljebb 1 MB, legfeljebb 255 tulajdonsággal (beleértve a `PartitionKey` , `RowKey` , és `Timestamp` ). |
| A `PartitionKey` |Legfeljebb 1 KB méretű sztring. |
| A `RowKey` |Legfeljebb 1 KB méretű sztring. |
| Entitás-csoport tranzakciójának mérete |Egy tranzakció legfeljebb 100 entitást tartalmazhat, és a hasznos adatnak 4 MB-nál kisebbnek kell lennie. Egy EGT csak egyszer tud frissíteni egy entitást. |

További információ: [a Table Service adatmodell ismertetése](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model).  

### <a name="cost-considerations"></a>Költségekkel kapcsolatos szempontok
A Table Storage szolgáltatás viszonylag olcsó, azonban a kapacitás kihasználtsága és a tranzakciók mennyisége a Table Storage szolgáltatást használó bármely megoldás kiértékelésének részeként is szerepelnie kell. Számos forgatókönyv esetében azonban a denormalizált vagy ismétlődő adatok tárolása a megoldás teljesítményének és méretezhetőségének javítása érdekében érvényes megközelítés. A díjszabással kapcsolatos további információkért lásd: az [Azure Storage díjszabása](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="guidelines-for-table-design"></a>Irányelvek táblatervezéshez
Ezek a lista összefoglalják a legfontosabb irányelveket, amelyeket érdemes szem előtt tartani a táblák tervezésekor. Ez az útmutató később részletesen ismerteti őket. Ezek az irányelvek különböznek azoktól az irányelvektől, amelyeket általában a kapcsolatok adatbázisának megtervezése követ.  

A Table Storage hatékony *olvasásának* megtervezése:

* **Az olvasási és a nagy teljesítményű alkalmazásokban való lekérdezés tervezése.** Amikor megtervezi a táblázatokat, gondolja át a lekérdezéseket (különösen a késésre érzékenyeket), mielőtt meggondolja, hogyan frissítheti az entitásokat. Ez általában egy hatékony és nagy teljesítményű megoldás eredményét eredményezi.  
* **Adja meg `PartitionKey` `RowKey` a és a lekérdezéseit is.** A *pontok lekérdezései* , például ezek a leghatékonyabb Table Storage-lekérdezések.  
* **Érdemes lehet az entitások duplikált példányait tárolni.** A Table Storage olcsó, ezért a hatékonyabb lekérdezések lehetővé tételéhez ugyanazt az entitást többször kell tárolni (különböző kulcsokkal).  
* **Érdemes lehet az adatai denormalizálása.** A Table Storage olcsó, ezért érdemes lehet az adatai denormalizálása. Tárolhatja például az összegző entitásokat, hogy az összesített adatlekérdezések csak egyetlen entitáshoz férhessenek hozzá.  
* **Használjon összetett kulcsos értékeket.** Az egyetlen kulcs, amelyet Ön `PartitionKey` és `RowKey` . Használjon például összetett kulcsos értékeket az entitásokhoz való helyettesítő hozzáférési útvonalak engedélyezéséhez.  
* **Lekérdezési leképezés használata.** Csökkentheti a hálózaton keresztül átvitt adatok mennyiségét olyan lekérdezések használatával, amelyek csak a szükséges mezőket választják ki.  

A tábla tárterületének megtervezése hatékony *írásra* :  

* **Ne hozzon létre gyors partíciót.** Válassza ki azokat a kulcsokat, amelyek lehetővé teszik a kérések több partíción keresztüli terjesztését egy adott időpontban.  
* **Kerülje a forgalomban lévő tüskéket.** Ossza el a forgalmat egy ésszerű időn belül, és kerülje a forgalomban lévő tüskéket.
* **Ne feltétlenül hozzon létre külön táblát az egyes entitások típusaihoz.** Ha az entitások típusai között atomi tranzakciókat igényel, akkor a több entitás típusát ugyanabban a partícióban tárolhatja ugyanabban a táblában.
* **Gondolja át, hogy mekkora átviteli sebességet kell elérnie.** Tisztában kell lennie a Table Storage skálázhatósági céljaival, és biztosítania kell, hogy a kialakítás ne lépje túl a táblázatot.  

Az útmutató későbbi részében olyan példákat talál, amelyek az összes alapelvet bevezetik a gyakorlatban.  

## <a name="design-for-querying"></a>Tervezés lekérdezéshez
A Table Storage nagy mennyiségű, írható vagy a kettő kombinációját képes olvasni. Ez a szakasz az olvasási műveletek hatékony támogatásának kialakítását veszi figyelembe. Az olvasási műveleteket hatékonyan támogató kialakítás általában írási műveletek esetén is hatékony. Az írási műveletek támogatásának tervezésekor azonban további szempontokat is figyelembe kell venni. Ezeket a következő, az [adatmódosításra szolgáló tervezési](#design-for-data-modification)szakaszban tárgyaljuk.

Egy jó kiindulási pont, amely lehetővé teszi, hogy az adatolvasások hatékonyak legyenek: "milyen lekérdezéseket kell futtatni az alkalmazásnak, hogy lekérje a szükséges adatgyűjtést?"  

> [!NOTE]
> A Table Storage szolgáltatással fontos, hogy a kialakítás elöl legyen, mivel nehéz és költséges lehet később módosítani. A kapcsolódó adatbázisban például gyakran lehetséges a teljesítménnyel kapcsolatos problémák megoldása, ha indexeket ad hozzá egy meglévő adatbázishoz. Ez a táblázatos tárolással nem lehetséges.  

### <a name="how-your-choice-of-partitionkey-and-rowkey-affects-query-performance"></a>A `PartitionKey` `RowKey` lekérdezés teljesítményének kiválasztása és hatása
Az alábbi példák azt feltételezik, hogy a Table Storage az alábbi struktúrával tárolja az alkalmazotti entitásokat (a legtöbb példa kihagyja a `Timestamp` tulajdonságot az érthetőség kedvéért):  

| Oszlop neve | Adattípus |
| --- | --- |
| `PartitionKey` (Részleg neve) |Sztring |
| `RowKey` (Alkalmazott azonosítója) |Sztring |
| `FirstName` |Sztring |
| `LastName` |Sztring |
| `Age` |Egész szám |
| `EmailAddress` |Sztring |

Íme néhány általános útmutató a Table Storage-lekérdezések tervezéséhez. A következő példákban használt szűrési szintaxis a Table Storage REST API. További információ: [lekérdezési entitások](/rest/api/storageservices/Query-Entities).  

* Az adott *pont lekérdezése* a leghatékonyabb keresés, amelyet a legalacsonyabb késleltetést igénylő nagy mennyiségű keresésekhez vagy keresésekhez ajánlott használni. Egy ilyen lekérdezés az indexeket használva hatékonyan megkeresheti az egyes entitásokat a és az értékek megadásával `PartitionKey` `RowKey` . Példa: `$filter=(PartitionKey eq 'Sales') and (RowKey eq '2')`.  
* A második legjobb a *tartomány lekérdezése*. A és a (z `PartitionKey` ) és a szűrők használatával `RowKey` több entitást ad vissza. Az `PartitionKey` érték azonosítja az adott partíciót, és az `RowKey` értékek a partícióban található entitások egy részhalmazát azonosítják. Példa: `$filter=PartitionKey eq 'Sales' and RowKey ge 'S' and RowKey lt 'T'`.  
* A harmadik legjobb a *partíciós vizsgálat*. A és a `PartitionKey` szűrőket használja egy másik nem kulcsfontosságú tulajdonsághoz, és több entitást is visszaadhat. Az `PartitionKey` érték azonosítja az adott partíciót, és a tulajdonságértékek a partícióban lévő entitások egy részhalmaza számára kiválasztva. Példa: `$filter=PartitionKey eq 'Sales' and LastName eq 'Smith'`.  
* A *tábla vizsgálata* nem tartalmazza a (z) `PartitionKey` , és nem hatékony, mert megkeresi az összes olyan partíciót, amelyik felkészíti a táblázatot a megfelelő entitásokra. Egy táblázatos vizsgálatot végez, függetlenül attól, hogy a szűrő a-t használja-e `RowKey` . Példa: `$filter=LastName eq 'Jones'`.  
* Az Azure Table Storage-lekérdezések, amelyek több entitást adnak vissza, `PartitionKey` és sorrendbe rendezik azokat `RowKey` . Ha nem szeretné, hogy az entitások ne legyenek az ügyfélben, válassza a leggyakoribb `RowKey` rendezési sorrendet definiáló elemet. A Azure Cosmos DB Azure Table API által visszaadott lekérdezési eredmények nem a partíciós kulcs vagy a sor kulcsa szerint vannak rendezve. A szolgáltatások közötti különbségek részletes listáját a [Azure Cosmos db és az Azure Table storage Table API közötti különbségek](table-api-faq.md#table-api-vs-table-storage)című részben tekintheti meg.

A "**vagy**" használatával egy szűrő megadásával határozhatja meg az értékek alapján egy `RowKey` partíció vizsgálatát, és nem kezelhető tartomány-lekérdezésként. Ezért Kerülje a szűrőket használó lekérdezéseket, például a következőt: `$filter=PartitionKey eq 'Sales' and (RowKey eq '121' or RowKey eq '322')` .  

A Storage ügyféloldali kódtárat használó ügyféloldali kódokra vonatkozó példákat a hatékony lekérdezések futtatásához lásd:  

* [Pont lekérdezésének futtatása a Storage ügyféloldali kódtár használatával](#run-a-point-query-by-using-the-storage-client-library)
* [Több entitás beolvasása a LINQ használatával](#retrieve-multiple-entities-by-using-linq)
* [Kiszolgálóoldali kivetítés](#server-side-projection)  

Az ugyanabban a táblában tárolt több entitást kezelő ügyféloldali kódokra vonatkozó Példákért lásd:  

* [Heterogén entitások típusának használata](#work-with-heterogeneous-entity-types)  

### <a name="choose-an-appropriate-partitionkey"></a>Megfelelő kiválasztása `PartitionKey`
Ön dönti el, hogy `PartitionKey` szükséges-e a EGTs használatának engedélyezése (az egységesség biztosítása érdekében) az entitások több partíción való terjesztésének követelménye között (a méretezhető megoldás biztosítása érdekében).  

Egyetlen szélsőséges esetben egyetlen partícióban tárolhatja az összes entitást. Ez azonban korlátozhatja a megoldás méretezhetőségét, és megakadályozhatja, hogy a tábla tárterülete ne tudja betölteni a kérelmeket. A másik végletben partícióként egy entitást is tárolhat. Ez a rugalmasan méretezhető, és lehetővé teszi a Table Storage számára, hogy terheléselosztási kérelmeket használjon, de megakadályozza az entitás-csoportok tranzakcióinak használatát.  

Az ideális `PartitionKey` megoldás lehetővé teszi, hogy hatékony lekérdezéseket használjon, és elegendő partícióval rendelkezik, hogy a megoldás skálázható legyen. Általában úgy találja, hogy az entitások megfelelő tulajdonsággal rendelkeznek, amely az entitásokat a megfelelő partíciók között osztja szét.

> [!NOTE]
> A felhasználókra vagy alkalmazottakra vonatkozó adatokat tároló rendszerekben például `UserID` jó lehet `PartitionKey` . Lehet, hogy több entitása is van, amelyek egy adott `UserID` partíciót használnak. Minden entitás, amely egy felhasználó adatait tárolja, egyetlen partícióba van csoportosítva. Ezek az entitások a EGTs-on keresztül érhetők el, miközben továbbra is rugalmasan méretezhetők.
> 
> 

További szempontokat is figyelembe `PartitionKey` kell vennie, amelyek az entitások beszúrásával, frissítésével és törlésével kapcsolatosak. További információt a cikk későbbi, [adatmódosítási terve](#design-for-data-modification) című szakaszában talál.  

### <a name="optimize-queries-for-table-storage"></a>A Table Storage-lekérdezések optimalizálása
A Table Storage automatikusan indexeli az entitásokat `PartitionKey` `RowKey` egy fürtözött indexben lévő és értékek használatával. Ennek az az oka, hogy a pontok lekérdezései a leghatékonyabbak. A és a fürtözött indexén azonban nincsenek indexek `PartitionKey` `RowKey` .

Számos tervnek meg kell felelnie az entitások több feltételen alapuló keresésének engedélyezéséhez szükséges követelményeknek. Például az alkalmazotti entitások az e-mailek, az alkalmazotti AZONOSÍTÓk vagy a vezetéknév alapján vannak megkeresve. A (z) szakasz [tábla kialakítási mintáinak](#table-design-patterns) következő mintái az ilyen típusú követelményeket kezelik. A minták azt is leírják, hogyan lehet megkerülni, hogy a Table Storage nem biztosít másodlagos indexeket.  

* [Partíción belüli másodlagos index minta](#intra-partition-secondary-index-pattern): az egyes entitások több példányának tárolása különböző `RowKey` értékek használatával (ugyanabban a partícióban). Ez lehetővé teszi a gyors és hatékony kereséseket, valamint az alternatív rendezési sorrendeket különböző `RowKey` értékek használatával.  
* [Partíciók közötti másodlagos index minta](#inter-partition-secondary-index-pattern): az egyes entitások több példányának tárolása különböző, `RowKey` különálló partíciókban vagy külön táblákban található értékek használatával. Ez lehetővé teszi a gyors és hatékony kereséseket, valamint az alternatív rendezési sorrendeket különböző `RowKey` értékek használatával.  
* [Entitások indexelése minta](#index-entities-pattern): az index entitások kezelése az entitások listáját visszaadó hatékony keresések engedélyezéséhez.  

### <a name="sort-data-in-table-storage"></a>Az Adatrendezés a Table Storage-ban

A Table Storage a lekérdezés eredményeit növekvő sorrendbe rendezi, a és a alapján `PartitionKey` `RowKey` .

> [!NOTE]
> A Azure Cosmos DB Azure Table API által visszaadott lekérdezési eredmények nem a partíciós kulcs vagy a sor kulcsa szerint vannak rendezve. A szolgáltatások közötti különbségek részletes listáját a [Azure Cosmos db és az Azure Table storage Table API közötti különbségek](table-api-faq.md#table-api-vs-table-storage)című részben tekintheti meg.

A Table Storage-ban található kulcsok karakterlánc-értékek. Annak érdekében, hogy a numerikus értékek megfelelően rendezve legyenek, konvertálja őket rögzített hosszba, és a nulla értékkel kell ellátni őket. Ha például az alkalmazott azonosító érték `RowKey` egész érték, akkor a **123** -as alkalmazott azonosító értékét a **00000123**-re kell konvertálnia. 

Számos alkalmazás rendelkezik a különböző megrendelésekben tárolt adatok használatára vonatkozó követelményekkel: például az alkalmazottak név szerinti rendezése vagy a csatlakozás dátuma. A (z) szakasz [táblázatának kialakítási mintái](#table-design-patterns) a következő mintákat ismertetik az entitások más rendezési rendeléseivel kapcsolatban:  

* [Partíción belüli másodlagos index minta](#intra-partition-secondary-index-pattern): az egyes entitások több példányának tárolása különböző `RowKey` értékek használatával (ugyanabban a partícióban). Ez lehetővé teszi a gyors és hatékony kereséseket, valamint az alternatív rendezési sorrendeket különböző `RowKey` értékek használatával.  
* [Partíciók közötti másodlagos index minta](#inter-partition-secondary-index-pattern): az egyes entitások több példányának tárolása különböző `RowKey` értékekkel külön táblákban. Ez lehetővé teszi a gyors és hatékony kereséseket, valamint az alternatív rendezési sorrendeket különböző `RowKey` értékek használatával.
* [Napló hátsó mintája](#log-tail-pattern): a partícióhoz legutóbb hozzáadott *n* entitások lekérése egy olyan `RowKey` értékkel, amely fordított dátum és idő sorrendbe rendezi a sort.  

## <a name="design-for-data-modification"></a>Tervezés adatmódosításhoz
Ez a szakasz a beszúrások, a frissítések és a törlések optimalizálására vonatkozó tervezési szempontokra összpontosít. Bizonyos esetekben ki kell értékelnie a kiértékelést olyan kialakítások között, amelyek optimalizálják az adatmódosításra optimalizált formatervezési minták lekérdezését. Ez a kiértékelés hasonló a viszonyítási adatbázisok kialakításához (bár a tervezési kompromisszumok kezelésének módszerei eltérőek a kapcsolódó adatbázisokban). A [tábla kialakítási mintái](#table-design-patterns) a táblázatos tárolással kapcsolatos részletes tervezési mintákat ismertetik, és kiemelik a jelen kompromisszumok némelyikét. A gyakorlatban azt tapasztalja, hogy számos, az entitások lekérdezésére optimalizált terv is jól működik az entitások módosításához.  

### <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>A beszúrási, frissítési és törlési műveletek teljesítményének optimalizálása
Entitások frissítéséhez vagy törléséhez a és az értékeket kell tudnia azonosítani `PartitionKey` `RowKey` . Ebben a tekintetben a választott `PartitionKey` és `RowKey` az entitások módosításának a különböző feltételeket kell követnie, hogy támogassa a pontok lekérdezéseit. Az entitásokat a lehető leghatékonyabb módon szeretné azonosítani. Nem érdemes nem hatékony partíciót vagy táblázatot keresni, hogy megkeresse az entitást a `PartitionKey` `RowKey` frissítéséhez vagy törléséhez szükséges értékek felderítése érdekében.  

A következő minták szerepelnek a [tábla kialakítási mintái](#table-design-patterns) című szakaszban, amely optimalizálja az INSERT, Update és DELETE műveletek teljesítményét:  

* [Nagy mennyiségű törlési minta](#high-volume-delete-pattern): engedélyezze a nagy mennyiségű entitás törlését úgy, hogy az összes entitást egyidejű törlésre tárolja a saját külön táblájában. Az entitásokat a tábla törlésével törölheti.  
* [Adatsorozat-minta](#data-series-pattern): a teljes adatsorozatok tárolása egyetlen entitásban, az Ön által végzett kérelmek számának csökkentése érdekében.  
* [Széles körű entitások minta](#wide-entities-pattern): több fizikai entitás használata a több mint 252 tulajdonságú logikai entitások tárolására.  
* [Nagyméretű entitások minta](#large-entities-pattern): a blob Storage használata nagy tulajdonságértékek tárolására.  

### <a name="ensure-consistency-in-your-stored-entities"></a>Konzisztencia biztosítása a tárolt entitásokban
A másik kulcsfontosságú tényező, amely hatással van az adatmódosítások optimalizálására kiválasztott kulcsokra, az Atomic Transactions használatával biztosítható a konzisztencia. A EGT csak akkor használhatók, ha ugyanazon a partíción tárolt entitásokon működnek.  

A következő minták szerepelnek a [táblázat kialakítási mintái](#table-design-patterns) a konzisztencia kezelése című szakaszban:  

* [Partíción belüli másodlagos index minta](#intra-partition-secondary-index-pattern): az egyes entitások több példányának tárolása különböző `RowKey` értékek használatával (ugyanabban a partícióban). Ez lehetővé teszi a gyors és hatékony kereséseket, valamint az alternatív rendezési sorrendeket különböző `RowKey` értékek használatával.  
* [Partíciók közötti másodlagos index minta](#inter-partition-secondary-index-pattern): az egyes entitások több példányának tárolása különböző, `RowKey` különálló partíciókban vagy külön táblákban található értékek használatával. Ez lehetővé teszi a gyors és hatékony kereséseket, valamint az alternatív rendezési sorrendeket különböző `RowKey` értékek használatával.  
* [Végül konzisztens tranzakciós minta](#eventually-consistent-transactions-pattern): az Azure Queues használatával a partíciós határok vagy a tárolási rendszerek határain belül végül konzisztens viselkedést engedélyezhet.
* [Entitások indexelése minta](#index-entities-pattern): az index entitások kezelése az entitások listáját visszaadó hatékony keresések engedélyezéséhez.  
* [Denormalizálás minta](#denormalization-pattern): a kapcsolódó adategységek összevonása egyetlen entitásban, hogy lehetővé váljon az összes szükséges adat lekérése egyetlen pont lekérdezéssel.  
* [Adatsorozat-minta](#data-series-pattern): a teljes adatsorozatok tárolása egyetlen entitásban, az Ön által végzett kérelmek számának csökkentése érdekében.  

További információ: entitás- [csoport tranzakciói](#entity-group-transactions) a cikk későbbi részében.  

### <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Győződjön meg arról, hogy a hatékony módosítások lehetővé teszik a tervezést a hatékony lekérdezésekhez
Sok esetben a hatékony lekérdezések kialakítása a hatékony módosítások eredményét eredményezi, azonban mindig ki kell értékelnie, hogy ez a helyzet az adott forgatókönyv esetén. A szakasz [tábla kialakítási mintáinak](#table-design-patterns) néhány mintája explicit módon értékeli ki az entitások lekérdezése és módosítása közötti kompromisszumokat, és mindig vegye figyelembe az egyes típusú műveletek számát.  

A (z) szakasz [táblázatának kialakítási mintái](#table-design-patterns) a következő mintákat ismertetik a hatékony lekérdezések tervezése és a hatékony adatmódosítás kialakítása között:  

* [Összetett kulcs mintája](#compound-key-pattern): összetett `RowKey` értékek használatával lehetővé teheti, hogy az ügyfél egyetlen pont lekérdezéssel keressen kapcsolódó adatokkal.  
* [Napló hátsó mintája](#log-tail-pattern): a partícióhoz legutóbb hozzáadott *n* entitások lekérése egy olyan `RowKey` értékkel, amely fordított dátum és idő sorrendbe rendezi a sort.  

## <a name="encrypt-table-data"></a>Tábla adatai titkosítása
A .NET Azure Storage ügyféloldali kódtára támogatja a karakterlánc-entitások tulajdonságainak az INSERT és a Replace műveletekhez való titkosítását. A titkosított karakterláncok a szolgáltatásban bináris tulajdonságokként tárolódnak, és a visszafejtés után vissza lesznek konvertálva karakterlánccá.    

A táblák esetében a titkosítási házirenden kívül a felhasználóknak meg kell adniuk a titkosítani kívánt tulajdonságokat. Adjon meg egy `EncryptProperty` attribútumot (a ból származó poco entitások esetében `TableEntity` ), vagy adjon meg egy titkosítási feloldót a kérések beállításai között. A titkosítási feloldó egy olyan delegált, amely egy partíciós kulcsot, egy sor kulcsot és egy tulajdonság nevét veszi át, és egy olyan logikai értéket ad vissza, amely jelzi, hogy a tulajdonságot titkosítani kell-e. A titkosítás során az ügyféloldali kódtár ezt az információt használja annak eldöntéséhez, hogy egy tulajdonságot titkosítani kell-e a drótba való írás során. A delegált emellett a tulajdonságok titkosítását is lehetővé teszi. (Ha például X, akkor titkosítsa az A tulajdonságot, máskülönben az A és B tulajdonságokat titkosítja.) Az entitások olvasása vagy lekérdezése során nem szükséges megadni ezeket az információkat.

Az egyesítés jelenleg nem támogatott. Mivel a tulajdonságok egy részhalmaza korábban egy másik kulccsal lett titkosítva, egyszerűen az új tulajdonságok egyesítése és a metaadatok frissítése adatvesztést eredményezhet. Az egyesítéshez szükség van további szolgáltatási hívásokra, hogy beolvassa a szolgáltatásból a már meglévő entitást, vagy egy tulajdonságot egy új kulcs használatával. Ezek egyike sem alkalmas a teljesítményre.     

További információ a táblák adatainak titkosításáról: [ügyféloldali titkosítás és Azure Key Vault Microsoft Azure Storagehoz](../storage/common/storage-client-side-encryption.md).  

## <a name="model-relationships"></a>Modell kapcsolatai
A tartományi modellek kiépítése kulcsfontosságú lépés a komplex rendszerek kialakításában. Általában a modellezési folyamattal azonosíthatók az entitások és a köztük fennálló kapcsolatok, így megismerhetik az üzleti tartományt, és tájékoztatják a rendszer kialakításáról. Ez a szakasz azt ismerteti, hogyan lehet lefordítani a tartományi modellekben található általános kapcsolattípus-típusokat a Table Storage kialakításához. A logikai adatmodellből fizikai NoSQL-alapú adatmodellre való hozzárendelési folyamat eltér a kapcsolatok adatbázisának tervezésekor használttól. A viszonyítási adatbázisok kialakítása általában feltételezi, hogy a redundancia minimalizálása érdekében optimalizált adatok normalizálása folyamatban van. Az ilyen kialakítás azt is feltételezi, hogy a deklaratív lekérdezési képesség az adatbázis működésének megvalósítását absztrakt módon mutatja be.  

### <a name="one-to-many-relationships"></a>Egy-a-többhöz kapcsolat
Gyakran fordulnak elő egy-a-többhöz kapcsolat az üzleti tartomány objektumai között: például az egyik részleg számos alkalmazottal rendelkezik. Több módon is megvalósítható egy-a-többhöz kapcsolat a Table Storage-ban, amelyek mindegyike az adott forgatókönyvhöz kapcsolódó előnyeit és hátrányait is tartalmazza.  

Vegyünk példaként egy nagyméretű multinacionális vállalatot, amely több tízezer szervezeti egységgel és alkalmazotti egységgel rendelkezik. Minden részlegnek számos alkalmazottja van, és mindegyik alkalmazott egy adott részleghez van társítva. Az egyik módszer a különálló részleg és az alkalmazott entitások tárolása, például a következők:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE01.png" alt-text="Részleg entitást és alkalmazotti entitást bemutató ábra":::

Ez a példa egy implicit, egy-a-többhöz kapcsolatot mutat be a típusok között az `PartitionKey` érték alapján. Minden részlegnek számos alkalmazottja lehet.  

Ez a példa egy részleg entitást és a hozzá tartozó alkalmazott entitásokat is megjeleníti ugyanabban a partícióban. Dönthet úgy, hogy különböző partíciókat, táblákat vagy akár tárolási fiókokat is használhat a különböző entitások típusaihoz.  

Egy másik módszer az adatai denormalizálása, és csak a denormalizált részleg adataival rendelkező alkalmazotti entitások tárolása, ahogy az az alábbi példában is látható. Ebben a konkrét esetben ez a denormalizált megközelítés nem a legjobb, ha olyan követelmény, hogy módosítani tudja a Department Manager részleteit. Ehhez frissítenie kell a részleg összes alkalmazottját.  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE02.png" alt-text="Az alkalmazotti entitás ábrája, amely bemutatja, hogyan lehet denormalizálni az adatait, és csak az alkalmazotti entitásokat tárolja a denormalizált részleg adataival.":::

További információ: a [denormalizálás mintája](#denormalization-pattern) az útmutató későbbi részében.  

Az alábbi táblázat összefoglalja az alkalmazotti és részlegi entitások egy-a-többhöz kapcsolattal rendelkező tárolási módszereinek előnyeit és hátrányait. Azt is érdemes figyelembe venni, hogy milyen gyakran várható a különböző műveletek végrehajtása. Lehetséges, hogy olyan kialakítással rendelkezik, amely költséges műveletet tartalmaz, ha a művelet csak ritkán fordul elő.  

<table>
<tr>
<th>Módszer</th>
<th>Előnyök</th>
<th>Hátrányok</th>
</tr>
<tr>
<td>Különálló entitások típusai, azonos partíció, azonos tábla</td>
<td>
<ul>
<li>Egyetlen művelettel frissítheti a részleg entitásait.</li>
<li>Az EGT segítségével megtarthatja az egységességet, ha az alkalmazotti entitás frissítésekor/beszúrásakor/törlésekor követelmény, hogy módosítania kell egy részleg entitását. Ha például az egyes részlegek esetében fenntart egy részleg alkalmazottainak a darabszámát.</li>
</ul>
</td>
<td>
<ul>
<li>Előfordulhat, hogy egy alkalmazott és egy részleg entitást is le kell kérnie néhány ügyfél-tevékenységhez.</li>
<li>A tárolási műveletek ugyanabban a partícióban történnek. A nagy tranzakciós kötetek esetében ez egy hotspotot eredményezhet.</li>
<li>Egy alkalmazott nem helyezhető át egy új részlegbe egy EGT használatával.</li>
</ul>
</td>
</tr>
<tr>
<td>Különálló entitások típusai, különböző partíciók vagy táblák vagy Storage-fiókok</td>
<td>
<ul>
<li>Egyetlen művelettel frissítheti a részleg entitásait vagy az alkalmazott entitásokat.</li>
<li>A nagy tranzakciós kötetek esetében ez segíthet a terhelés több partíción való elosztásában.</li>
</ul>
</td>
<td>
<ul>
<li>Előfordulhat, hogy egy alkalmazott és egy részleg entitást is le kell kérnie néhány ügyfél-tevékenységhez.</li>
<li>A EGTs nem használható a konzisztencia fenntartására egy alkalmazott frissítésekor/behelyezése/törlése és a részleg frissítése során. Például egy részleg entitásban lévő alkalmazottak számának frissítése.</li>
<li>Egy alkalmazott nem helyezhető át egy új részlegbe egy EGT használatával.</li>
</ul>
</td>
</tr>
<tr>
<td>Normalizálás egyetlen entitás típusúra</td>
<td>
<ul>
<li>Az összes szükséges információt egyetlen kérelemmel kérheti le.</li>
</ul>
</td>
<td>
<ul>
<li>Költséges lehet a konzisztencia fenntartása, ha frissítenie kell a részleg adatait (ez a részleg összes alkalmazottjának frissítését igényli).</li>
</ul>
</td>
</tr>
</table>

A lehetőségek közül választhat, és az előnyeit és hátrányait a legjelentősebb mértékben az adott alkalmazási helyzettől függ. Például milyen gyakran módosítja a részleg entitásait? Az alkalmazotti lekérdezéseknek szüksége van a további részlegi információkra? Hogyan közelíti meg a partíciók skálázhatósági korlátait vagy a Storage-fiókját?  

### <a name="one-to-one-relationships"></a>Egy-az-egyhez kapcsolat
A tartományi modellek tartalmazhatnak egy-az-egyhez kapcsolatot az entitások között. Ha egy-az-egyhez kapcsolatot kell létrehoznia a Table Storage-ban, akkor azt is ki kell választania, hogyan csatolja a két kapcsolódó entitást, ha mindkettőt le kell kérnie. Ez a hivatkozás implicit lehet a kulcs értékeinek konvenciója alapján, vagy explicit módon, ha egy hivatkozást tárol az `PartitionKey` `RowKey` egyes entitásokban lévő és értékekkel a kapcsolódó entitáshoz. Ha meg szeretné tudni, hogy a kapcsolódó entitásokat ugyanabban a partícióban kell tárolnia, tekintse meg az [egy-a-többhöz kapcsolatok](#one-to-many-relationships)című szakaszt.  

Léteznek olyan implementációs megfontolások is, amelyek egy-az-egyhez kapcsolatok megvalósítására vezethetnek a Table Storage-ban:  

* Nagyméretű entitások kezeléséhez (További információ: [nagyméretű entitások mintája](#large-entities-pattern)).  
* Hozzáférés-vezérlések implementálása (További információ: [hozzáférés szabályozása közös hozzáférési aláírásokkal](#control-access-with-shared-access-signatures)).  

### <a name="join-in-the-client"></a>Csatlakozás az ügyfélen
Bár a Table Storage-ban több mód is van a kapcsolatok modellezésére, ne feledje, hogy a Table Storage használatának két fő oka a méretezhetőség és a teljesítmény. Ha úgy találja, hogy számos olyan kapcsolatot modellez, amely veszélyezteti a megoldás teljesítményét és méretezhetőségét, érdemes megkérdezni, hogy szükség van-e az összes adatkapcsolat létrehozására a tábla kialakításában. Lehet, hogy leegyszerűsíti a kialakítást, és javítja a megoldás méretezhetőségét és teljesítményét, ha lehetővé teszi, hogy az ügyfélalkalmazás bármilyen szükséges illesztést végezzen.  

Ha például olyan kis táblákat használ, amelyek nem változnak gyakran előforduló, akkor egyszer lekérheti ezeket az adatfájlokat, és gyorsítótárazhatja azt az ügyfélen. Ezzel elkerülhető, hogy az ismétlődő adatpontok ugyanazt az adatlekérdezést tudják lekérni. A jelen Útmutatóban bemutatott példákban a kisméretű szervezetek készlete valószínűleg kicsi, és ritkán változik. Ez jó választást tesz lehetővé, hogy az ügyfélalkalmazás egyszer letöltse a keresési adatként, és gyorsítótárazza azokat.  

### <a name="inheritance-relationships"></a>Öröklési kapcsolatok
Ha az ügyfélalkalmazás olyan osztályok készletét használja, amelyek egy öröklési kapcsolat részét képezik az üzleti entitások képviseletére, egyszerűen megtarthatja ezeket az entitásokat a Table Storage-ban. Előfordulhat például, hogy a következő osztályok vannak definiálva az ügyfélalkalmazás számára, ahol a `Person` egy absztrakt osztály.

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE03.png" alt-text="Öröklési kapcsolatok diagramja":::

A Table Storage két konkrét osztályának példányait egyetlen tábla használatával megtarthatja `Person` . Használjon az alábbihoz hasonló entitásokat:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE04.png" alt-text="Az ügyfél entitását és az alkalmazotti entitást bemutató ábra":::

További információ az ügyfél kódjában található több entitás típusának használatáról: [heterogén entitások típusainak](#work-with-heterogeneous-entity-types) használata az útmutató későbbi részében. Ez példákat tartalmaz arra, hogyan ismerhető fel az entitás típusa az ügyfél kódjában.  

## <a name="table-design-patterns"></a>Táblatervezési minták
Az előző szakaszban megtanulta, hogyan optimalizálhatja a tábla kialakítását az entitások adatainak lekérdezésekkel való beolvasásához, illetve az entitások adatainak beszúrásához, frissítéséhez és törléséhez. Ez a szakasz a Table Storage-hoz való használatra alkalmas mintákat ismerteti. Emellett azt is megtudhatja, hogyan lehet gyakorlatilag az útmutató korábbi részében ismertetett problémákat és kompromisszumokat kezelni. A következő ábra a különböző minták közötti kapcsolatokat összegzi:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE05.png" alt-text="A tábla kialakítási mintáinak ábrája":::

A minta Térkép kiemeli az útmutatóban ismertetett minták (kék) és a (narancssárga) mintázatok közötti kapcsolatokat. Természetesen számos más mintát érdemes figyelembe venni. A Table Storage egyik fő forgatókönyve például az, hogy a [parancs lekérdezési felelősségének elkülönítési](/previous-versions/msp-n-p/jj554200(v=pandp.10)) mintája alapján az [anyagilag megadott nézet mintát](/previous-versions/msp-n-p/dn589782(v=pandp.10)) használja.  

### <a name="intra-partition-secondary-index-pattern"></a>Partíción belüli másodlagos index minta
Az egyes entitások több példányának tárolása különböző `RowKey` értékek használatával (ugyanabban a partícióban). Ez lehetővé teszi a gyors és hatékony kereséseket, valamint az alternatív rendezési sorrendeket különböző `RowKey` értékek használatával. A másolatok közötti frissítések konzisztensek lehetnek a EGTs használatával.  

#### <a name="context-and-problem"></a>Kontextus és probléma
A Table Storage automatikusan indexeli az entitásokat a `PartitionKey` és `RowKey` értékek használatával. Ez lehetővé teszi, hogy az ügyfélalkalmazás hatékonyan lekérje az entitásokat ezeknek az értékeknek a használatával. Például a következő táblázat struktúrájának használatával az ügyfélalkalmazás egy pont lekérdezéssel kérheti le az egyes alkalmazotti entitásokat a részleg neve és az alkalmazott azonosítója (a `PartitionKey` és `RowKey` értékek) használatával. Az ügyfelek az egyes részlegeken belül az alkalmazotti azonosító szerint rendezett entitásokat is lekérhetik.

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE06.png" alt-text="Az alkalmazott entitás ábrája, amelyben az ügyfélalkalmazás egy pont lekérdezés használatával lekérheti az egyes alkalmazotti entitásokat a részleg nevével és az alkalmazott azonosítójával (a PartitionKey és a RowKey értékekkel).":::

Ha egy másik tulajdonság (például az e-mail-cím) alapján egy alkalmazotti entitást is szeretne találni, akkor az egyezés megtalálásához kevésbé hatékony partíciós vizsgálatot kell használnia. Ennek az az oka, hogy a Table Storage nem biztosít másodlagos indexeket. Emellett nincs lehetőség arra, hogy az alkalmazottak listáját a sorrend szerint eltérő sorrendbe sorolja `RowKey` .  

#### <a name="solution"></a>Megoldás
A másodlagos indexek hiányának megkerülése érdekében az egyes entitások több példányát is tárolhatja, az egyes másolatokat pedig egy másik `RowKey` érték használatával. Ha egy entitást a következő struktúrákkal tárol, akkor az e-mail-cím vagy az alkalmazott azonosítója alapján hatékonyan lekérheti az alkalmazotti entitásokat. A `RowKey` ,, `empid_` és `email_` lehetővé teszi, hogy egyetlen alkalmazott vagy egy több alkalmazott lekérdezését használja az e-mail-címek vagy az alkalmazotti azonosítók használatával.  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE07.png" alt-text="Az alkalmazotti entitást különböző RowKey értékekkel ábrázoló ábra":::

A következő két szűrési feltétel (az alkalmazotti azonosító alapján felkeresett és egy e-mail-cím alapján felkeresett) a pontok lekérdezéseit is megadja:  

* $filter = (PartitionKey EQ "Sales") és (RowKey EQ "empid_000223")  
* $filter = (PartitionKey EQ ' Sales ') és (RowKey EQ ' email_jonesj@contoso.com ')  

Ha az alkalmazotti entitások egy tartományát kérdezi le, megadhatja az alkalmazotti azonosító sorrendjét, illetve az e-mail-címek sorrendje szerint rendezett tartományt. A megfelelő előtaggal rendelkező entitások lekérdezése a alkalmazásban `RowKey` .  

* Az értékesítési részleg összes alkalmazottjának a 000100 – 000199 tartományba tartozó alkalmazotti AZONOSÍTÓjának megkereséséhez használja a következőt: $filter = (PartitionKey EQ ' Sales ') és (RowKey GE ' empid_000100 ') és (RowKey le ' empid_000199 ')  
* Az értékesítési részleg összes alkalmazottjának az "a" betűvel kezdődő e-mail-címmel történő megkereséséhez használja a következőt: $filter = (PartitionKey EQ "Sales") és (RowKey GE "email_a") és (RowKey lt "email_b")  
  
Az előző példákban használt szűrési szintaxis a Table Storage REST API. További információ: [lekérdezési entitások](/rest/api/storageservices/Query-Entities).  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* A Table Storage szolgáltatás viszonylag olcsó a használathoz, így a duplikált adatokat tároló költségek terhelése nem lehet jelentős. A várható tárolási követelmények alapján azonban mindig értékelnie kell a terv költségeit, és csak ismétlődő entitásokat kell hozzáadnia az ügyfélalkalmazás által futtatott lekérdezések támogatásához.  
* Mivel a másodlagos index entitások ugyanabban a partícióban vannak tárolva, mint az eredeti entitások, ügyeljen arra, hogy ne lépje túl az egyes partíciók méretezhetőségi célkitűzéseit.  
* A EGTs használatával megtarthatja a duplikált entitások egymással való egységességét, ha az entitás két példányát atomian frissíti. Ez azt jelenti, hogy az entitások összes példányát ugyanabban a partícióban kell tárolnia. További információ: [Entity Transactions-tranzakciók használata](#entity-group-transactions).  
* A tulajdonsághoz használt értéknek `RowKey` egyedinek kell lennie az egyes entitásokhoz. Használjon összetett kulcsos értékeket.  
* A numerikus értékek `RowKey` (például a 000223 alkalmazott azonosítója) kitöltése lehetővé teszi a megfelelő rendezést és szűrést a felső és alsó határok alapján.  
* Nem feltétlenül szükséges az entitás összes tulajdonságának duplikálása. Ha például az entitásokat az e-mail-cím használatával megkereső lekérdezések `RowKey` nem szükségesek az alkalmazottak korához, akkor ezek az entitások a következő szerkezettel rendelkezhetnek:

  :::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE08.png" alt-text="Alkalmazotti entitás ábrája":::

* Általában jobb az ismétlődő adattárolás, és gondoskodni arról, hogy egyetlen lekérdezéssel kérdezze le az összes szükséges adatkérést, mint ha egyetlen lekérdezés használatával keres egy entitást, és egy másikat a szükséges értékek megkereséséhez.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Használja ezt a mintát, ha:

- Az ügyfélalkalmazás számos különböző kulccsal kell lekérnie az entitásokat.
- Az ügyfélnek különböző rendezési sorrendbe kell beolvasnia az entitásokat.
- Az egyes entitásokat számos egyedi érték használatával azonosíthatja.

Azonban ügyeljen arra, hogy ne lépje túl a particionálási korlátokat, ha a különböző értékek használatával hajtja végre az entitások keresését `RowKey` .  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Partíciók közötti másodlagos index mintája](#inter-partition-secondary-index-pattern)
* [Összetett kulcs mintája](#compound-key-pattern)
* [Entitás-csoport tranzakciói](#entity-group-transactions)
* [Heterogén entitások típusának használata](#work-with-heterogeneous-entity-types)

### <a name="inter-partition-secondary-index-pattern"></a>Partíciók közötti másodlagos index mintája
Több példányban tárolhatja az egyes entitásokat különböző `RowKey` értékekkel külön partíciókban vagy külön táblákban. Ez lehetővé teszi a gyors és hatékony kereséseket, valamint az alternatív rendezési sorrendeket különböző `RowKey` értékek használatával.  

#### <a name="context-and-problem"></a>Kontextus és probléma
A Table Storage automatikusan indexeli az entitásokat a `PartitionKey` és `RowKey` értékek használatával. Ez lehetővé teszi, hogy az ügyfélalkalmazás hatékonyan lekérje az entitásokat ezeknek az értékeknek a használatával. Például a következő táblázat struktúrájának használatával az ügyfélalkalmazás egy pont lekérdezéssel kérheti le az egyes alkalmazotti entitásokat a részleg neve és az alkalmazott azonosítója (a `PartitionKey` és `RowKey` értékek) használatával. Az ügyfelek az egyes részlegeken belül az alkalmazotti azonosító szerint rendezett entitásokat is lekérhetik.  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE09.png" alt-text="Az alkalmazotti entitás struktúrájának ábrája, amely használat esetén az ügyfélalkalmazás egy pont lekérdezés használatával lekérheti az egyes alkalmazotti entitásokat a részleg nevével és az alkalmazott azonosítóval (a PartitionKey és a RowKey értékekkel)."::: 9

Ha egy másik tulajdonság (például az e-mail-cím) alapján szeretné megkeresni az alkalmazotti entitást, akkor kevésbé hatékony partíciós vizsgálatot kell használnia, hogy megtalálja a megfelelőt. Ennek az az oka, hogy a Table Storage nem biztosít másodlagos indexeket. Emellett nincs lehetőség arra, hogy az alkalmazottak listáját a sorrend szerint eltérő sorrendbe sorolja `RowKey` .  

Nagy mennyiségű tranzakciót számít fel ezen entitások ellen, és csökkenteni szeretné az ügyfelet korlátozó Table Storage-díj kockázatát.  

#### <a name="solution"></a>Megoldás
A másodlagos indexek hiányának megkerülése érdekében az egyes entitások több példányát is tárolhatja, az egyes másolatokat pedig különböző `PartitionKey` és `RowKey` értékek használatával. Ha egy entitást a következő struktúrákkal tárol, akkor az e-mail-cím vagy az alkalmazott azonosítója alapján hatékonyan lekérheti az alkalmazotti entitásokat. A `PartitionKey` ,, `empid_` és `email_` lehetővé teszi, hogy meghatározza, melyik indexet szeretné használni a lekérdezésekhez.  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE10.png" alt-text="Az elsődleges indextel és a másodlagos indexszel rendelkező alkalmazott entitással rendelkező alkalmazotti entitást ábrázoló ábra":::

A következő két szűrési feltétel (az alkalmazotti azonosító alapján felkeresett és egy e-mail-cím alapján felkeresett) a pontok lekérdezéseit is megadja:  

* $filter = (PartitionKey EQ "empid_Sales") és (RowKey EQ "000223")
* $filter = (PartitionKey EQ "email_Sales") és (RowKey EQ " jonesj@contoso.com ")  

Ha az alkalmazotti entitások egy tartományát kérdezi le, megadhatja az alkalmazotti azonosító sorrendjét, illetve az e-mail-címek sorrendje szerint rendezett tartományt. A megfelelő előtaggal rendelkező entitások lekérdezése a alkalmazásban `RowKey` .  

* Az értékesítési részleg összes alkalmazottjának a **000100** – **000199** tartományba tartozó alkalmazotti azonosítójának megkereséséhez, az alkalmazotti azonosító sorrendbe rendezéséhez használja a következőt: $Filter = (PartitionKey EQ "empid_Sales") és (RowKey GE "000100") és (RowKey le "000199")  
* Az értékesítési részleg összes alkalmazottja számára egy e-mail-címmel ellátott e-mail-cím megkeresése, amely az e-mail-címek sorrendjét használja: $filter = (PartitionKey EQ "email_Sales") és (RowKey GE "a") és (RowKey lt "b")  

Vegye figyelembe, hogy az előző példákban használt szűrési szintaxis a Table Storage REST API. További információ: [lekérdezési entitások](/rest/api/storageservices/Query-Entities).  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Megtarthatja, hogy az ismétlődő entitások végül konzisztensek legyenek egymással, ha a [végül konzisztens tranzakciós mintát](#eventually-consistent-transactions-pattern) használják az elsődleges és a másodlagos index entitások karbantartásához.  
* A Table Storage szolgáltatás viszonylag olcsó a használathoz, így a duplikált adatokat tároló költségek terhelése nem lehet jelentős aggodalom. Azonban mindig értékelje a terv költségeit a várható tárolási követelmények alapján, és csak duplikált entitásokat adjon hozzá az ügyfélalkalmazás által futtatott lekérdezések támogatásához.  
* A tulajdonsághoz használt értéknek `RowKey` egyedinek kell lennie az egyes entitásokhoz. Használjon összetett kulcsos értékeket.  
* A numerikus értékek `RowKey` (például a 000223 alkalmazott azonosítója) kitöltése lehetővé teszi a megfelelő rendezést és szűrést a felső és alsó határok alapján.  
* Nem feltétlenül szükséges az entitás összes tulajdonságának duplikálása. Ha például az entitásokat az e-mail-cím használatával megkereső lekérdezések `RowKey` nem szükségesek az alkalmazottak korához, akkor ezek az entitások a következő szerkezettel rendelkezhetnek:
  
  :::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE11.png" alt-text="A másodlagos indexszel rendelkező alkalmazotti entitást bemutató ábra":::

* Általában jobb, ha ismétlődő adattárolást végez, és biztosítja, hogy egyetlen lekérdezéssel kérdezze le az összes szükséges adatfájlt, mint ha egy lekérdezést használ egy entitás megkereséséhez a másodlagos index használatával, egy másikat pedig az elsődleges indexben található szükséges értékek megkereséséhez.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Használja ezt a mintát, ha:

- Az ügyfélalkalmazás számos különböző kulccsal kell lekérnie az entitásokat.
- Az ügyfélnek különböző rendezési sorrendbe kell beolvasnia az entitásokat.
- Az egyes entitásokat számos egyedi érték használatával azonosíthatja.

Akkor használja ezt a mintát, ha el szeretné kerülni, hogy a partíciók skálázhatósági korlátai meghaladják a különböző értékek használatával végzett entitás-keresési korlátokat `RowKey` .  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Végül konzisztens tranzakciós minta](#eventually-consistent-transactions-pattern)  
* [Partíción belüli másodlagos index minta](#intra-partition-secondary-index-pattern)  
* [Összetett kulcs mintája](#compound-key-pattern)  
* [Entitás-csoport tranzakciói](#entity-group-transactions)  
* [Heterogén entitások típusának használata](#work-with-heterogeneous-entity-types)  

### <a name="eventually-consistent-transactions-pattern"></a>Végül konzisztens tranzakciós minta
Az Azure Queues használatával engedélyezheti a partíciós határok vagy a tárolási rendszerek határain belüli, végül konzisztens viselkedést.  

#### <a name="context-and-problem"></a>Kontextus és probléma
A EGTs lehetővé teszi az atomi tranzakciók több entitás között, amelyek ugyanazt a partíciós kulcsot használják. A teljesítményre és méretezhetőségre vonatkozó okok miatt dönthet úgy, hogy külön partíciókban vagy különálló tárolási rendszeren tárolja a konzisztencia-követelményekkel rendelkező entitásokat. Ilyen esetben a EGTs nem használható a konzisztencia fenntartásához. Előfordulhat például, hogy meg kell őriznie a végleges konzisztencia fenntartásának követelményét:  

* Ugyanazon tábla két különböző partíciójában, különböző táblákban vagy különböző tárolási fiókokban tárolt entitások.  
* A Table Storage-ban és a blob Storage-ban tárolt blobokban tárolt entitások.  
* A Table Storage szolgáltatásban és a fájlrendszerben található fájlokban tárolt entitások.  
* A Table Storage-ban tárolt entitás, amely még az Azure Cognitive Search használatával van indexelve.  

#### <a name="solution"></a>Megoldás
Az Azure Queues használatával olyan megoldást valósíthat meg, amely két vagy több partícióra vagy tárolási rendszerre kiterjedő végleges konzisztenciát biztosít.

Ennek a megközelítésnek a szemléltetéséhez feltételezhető, hogy a korábbi alkalmazotti entitások archiválására van szükség. A korábban alkalmazott entitásokat ritkán kérdezik le, és ki kell zárni azokat a tevékenységeket, amelyek az aktuális alkalmazottakkal foglalkoznak. Ennek a követelménynek a megvalósításához az **aktuális** táblában és a korábbi alkalmazottaknál az **archív** táblában tárolja az aktív alkalmazottakat. Az alkalmazottak archiválásához törölnie kell az entitást az **aktuális** táblából, és hozzá kell adnia az entitást az **archív** táblához.

Azonban nem használhat EGT a két művelet végrehajtásához. Annak elkerülése érdekében, hogy a hiba miatt egy entitás mindkét vagy egyik táblában megjelenjen, az archiválási műveletnek végül konzisztensnek kell lennie. A következő Sequence diagram a művelet lépéseit ismerteti.  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE12.png" alt-text="A végleges konzisztencia megoldási diagramja":::

Az ügyfél az archiválási műveletet úgy indítja el, hogy egy üzenetet helyez el egy Azure-várólistán (ebben a példában az Employee #456 archiválásához). Egy feldolgozói szerepkör lekérdezi az új üzenetek várólistáját; Ha megtalál egyet, beolvassa az üzenetet, és elhagy egy rejtett másolatot a várólistán. A következő feldolgozói szerepkör beolvassa az entitás egy másolatát az **aktuális** táblából, beszúr egy másolatot az **archív** táblába, majd törli az eredetit az **aktuális** táblából. Végül, ha az előző lépések nem voltak hibák, a feldolgozói szerepkör törli a rejtett üzenetet a várólistából.  

Ebben a példában a diagram 4. lépése beszúrja az alkalmazottat az **archív** táblába. Felveheti az alkalmazottat blob Storage-tárolóba vagy fájlrendszerbeli fájlba.  

#### <a name="recover-from-failures"></a>Helyreállítás hibákból
Fontos, hogy a diagram 4-5-es lépéseiben szereplő műveletek *idempotens* abban az esetben, ha a feldolgozói szerepkörnek újra kell indítania az archiválási műveletet. Ha Table Storage-t használ, a 4. lépésnél használjon "INSERT vagy replace" műveletet; az 5. lépésben a használt ügyfél-függvénytár "Törlés, ha létezik" műveletét kell használnia. Ha más tárolási rendszereket használ, a megfelelő idempotens műveletet kell használnia.  

Ha a feldolgozói szerepkör soha nem fejezi be a 6. lépést a diagramon, akkor időtúllépés után az üzenet újból megjelenik a feldolgozói szerepkörben az újrafeldolgozásra váró várólistán. A feldolgozói szerepkör megtekintheti, hogy a várólistán lévő üzenetek hányszor lettek elolvasva, és ha szükséges, akkor a vizsgálathoz egy külön várólistára küldje el a "méreg" üzenetet. A várólista-üzenetek olvasásával és a sorok számának ellenőrzésével kapcsolatos további információkért lásd: [üzenetek](/rest/api/storageservices/Get-Messages)beolvasása.  

A Table Storage és a üzenetsor-tároló bizonyos hibái átmeneti hibák, és az ügyfélalkalmazás megfelelő újrapróbálkozási logikát is tartalmaz a kezeléséhez.  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Ez a megoldás nem biztosítja a tranzakciók elkülönítését. Előfordulhat például, hogy egy ügyfél beolvassa az **aktuális** és az **archív** táblát, amikor a feldolgozói szerepkör a diagram 4-5-es lépései között volt, és az adatok inkonzisztens nézete látható. Az adategységek végül konzisztensek lesznek.  
* Győződjön meg arról, hogy az 4-5-es lépések idempotens a végleges konzisztencia biztosítása érdekében.  
* A megoldás több várólista és feldolgozói szerepkör-példány használatával is méretezhető.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Akkor használja ezt a mintát, ha a különböző partíciókban vagy táblákban található entitások közötti végleges konzisztenciát szeretné garantálni. Ezt a mintát kiterjesztheti, így biztosíthatja a tábla-és blob-tárolók, valamint más nem Azure Storage-adatforrások (például adatbázisok vagy fájlrendszer) közötti műveletek végleges egységességét.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Entitás-csoport tranzakciói](#entity-group-transactions)  
* [Egyesítés vagy csere](#merge-or-replace)  

> [!NOTE]
> Ha a tranzakció elkülönítése fontos a megoldás számára, érdemes lehet áttervezni a táblákat, hogy lehetővé váljon a EGTs használata.  
> 
> 

### <a name="index-entities-pattern"></a>Entitások indexelése minta
Az entitások listáját visszaadó hatékony keresések engedélyezéséhez indexelő entitásokat kell fenntartani.  

#### <a name="context-and-problem"></a>Kontextus és probléma
A Table Storage automatikusan indexeli az entitásokat a `PartitionKey` és `RowKey` értékek használatával. Ez lehetővé teszi, hogy az ügyfélalkalmazás hatékonyan kérdezze le az entitásokat egy pont lekérdezés használatával. Például a következő táblázat struktúrájának használatával az ügyfélalkalmazás hatékonyan lekérheti az egyes alkalmazotti entitásokat a részleg neve és az alkalmazott azonosítója (a és a `PartitionKey` `RowKey` ) használatával.  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE13.png" alt-text="Az alkalmazotti entitás szerkezetének ábrája, amelyben az ügyfélalkalmazás hatékonyan lekérheti az egyes alkalmazotti entitásokat a részleg neve és az alkalmazott azonosítója (a PartitionKey és a RowKey) használatával.":::

Ha azt is szeretné, hogy az alkalmazott entitások listáját egy másik nem egyedi tulajdonság értéke alapján is le tudja kérni, például a vezetéknevet, akkor kevésbé hatékony partíciós vizsgálatot kell használnia. Ez a vizsgálat megkeresi a találatokat, és nem használja az indexet közvetlenül a kereséshez. Ennek az az oka, hogy a Table Storage nem biztosít másodlagos indexeket.  

#### <a name="solution"></a>Megoldás
Ha a vezetéknevet az előző entitás struktúrájával szeretné engedélyezni az utónévben, meg kell őriznie az alkalmazotti azonosítók listáját. Ha szeretné lekérni az alkalmazott entitásokat egy adott vezetéknevtel (például Jones), először meg kell keresnie a Jones-beli alkalmazottakhoz tartozó alkalmazotti azonosítók listáját a vezetéknevük alapján, majd le kell kérnie az alkalmazottak entitásait. Az alkalmazotti azonosítók listáját három fő lehetőséggel lehet tárolni:  

* BLOB Storage használata.  
* Hozzon létre index entitásokat az alkalmazott entitásokkal megegyező partícióban.  
* Hozzon létre index entitásokat egy külön partícióban vagy táblában.  

1. lehetőség: blob Storage használata  

Hozzon létre egy blobot minden egyedi vezetéknevhez, és minden blobban tárolja a `PartitionKey` vezetéknevét (osztály) és az alkalmazottak `RowKey` azonosítójának értékeit. Ha hozzáad vagy töröl egy alkalmazottat, győződjön meg arról, hogy a megfelelő blob tartalma végül konzisztens az alkalmazott entitásokkal.  

2. lehetőség: index entitások létrehozása ugyanabban a partícióban  

A következő adategységeket tároló indexelő entitások használata:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE14.png" alt-text="Az alkalmazott entitást bemutató ábra, amely tartalmazza az azonos vezetéknevű alkalmazottak azonosítóinak listáját.":::

A `EmployeeIDs` tulajdonság a ben tárolt utolsó névvel rendelkező alkalmazottak alkalmazotti azonosítóinak listáját tartalmazza `RowKey` .  

Az alábbi lépések azt ismertetik, hogy milyen eljárást kell követnie, amikor új alkalmazottat ad hozzá. Ebben a példában egy 000152-es AZONOSÍTÓJÚ alkalmazottat és vezetéknevet adunk hozzá az értékesítési részleghez:  

1. A `PartitionKey` "Sales" értékkel és a `RowKey` "Jones" értékkel olvassa be az index entitást. Mentse az entitás ETag a 2. lépésben való használatra.  
2. Hozzon létre egy Entity Group-tranzakciót (azaz egy batch-műveletet), amely beszúrja az új alkalmazott entitást ( `PartitionKey` érték: "Sales", `RowKey` érték: "000152"), és frissíti az index entitást (érték: " `PartitionKey` Sales", érték: " `RowKey` Jones"). A EGT ezt úgy teszi meg, hogy hozzáadja az új alkalmazott azonosítót a listához a EmployeeIDs mezőben. További információ a EGTs: [Entity Transactions](#entity-group-transactions).  
3. Ha a EGT egy optimista egyidejűségi hiba miatt meghiúsul (azaz valaki más módosította az index entitást), akkor az 1. lépésben kell megkezdenie.  

A második lehetőség használata esetén hasonló módszert használhat az alkalmazottak törlésére. Egy alkalmazott vezetéknevének módosítása valamivel összetettebb, mert olyan EGT kell futtatnia, amely három entitást frissít: az alkalmazott entitást, a régi vezetéknevű index entitást, valamint az új vezetéknevet index entitást. A módosítások elvégzése előtt le kell kérnie az egyes entitásokat, hogy lekérje a ETag értékeit, amelyeket a frissítések optimista Egyidejűség használatával történő végrehajtásához használhat.  

A következő lépések azt a folyamatot ismertetik, amelyet követnie kell, ha az összes alkalmazottat meg kell keresnie egy adott vezetéknevű részlegben. Ebben a példában a Sales részleg összes alkalmazottját megkeresi a vezetékneve Jones néven:  

1. A `PartitionKey` "Sales" értékkel és a `RowKey` "Jones" értékkel olvassa be az index entitást.  
2. Elemezheti az alkalmazotti azonosítók listáját a `EmployeeIDs` mezőben.  
3. Ha további információra van szüksége az alkalmazottakról (például az e-mail-címekről), a `PartitionKey` `RowKey` 2. lépésben beszerzett alkalmazottak listájából kérje le az egyes alkalmazottak entitásokat az "értékesítés" értékkel.  

3. lehetőség: index entitások létrehozása külön partícióban vagy táblázatban  

Ehhez a beállításhoz használja az indexelő entitásokat, amelyek a következő adategységeket tárolják:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE15.png" alt-text="Képernyőkép, amely megjeleníti az Employee index entitást, amely tartalmazza a RowKey és a PartitionKey tárolt utolsó névvel rendelkező alkalmazottak alkalmazotti azonosítóinak listáját.":::

A `EmployeeDetails` tulajdonság tartalmazza az alkalmazotti azonosítók és a részleg neve párok listáját a ben tárolt utolsó névvel rendelkező alkalmazottak számára `RowKey` .

A EGTs nem használható a konzisztencia fenntartásához, mert az index entitások az alkalmazott entitások külön partíciójában vannak. Győződjön meg arról, hogy az indexelő entitások végül konzisztensek az alkalmazott entitásokkal.  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Ehhez a megoldáshoz legalább két lekérdezésre van szükség a megfelelő entitások lekéréséhez: az egyiket, hogy lekérdezze az index entitásokat az értékek listájának beolvasásához `RowKey` , majd lekérdezi az egyes entitásokat a listában.  
* Mivel az egyes entitások maximális mérete 1 MB, a megoldás 2. és 3. beállítása feltételezi, hogy az adott vezetéknevhez tartozó alkalmazotti azonosítók listája soha nem nagyobb, mint 1 MB. Ha az alkalmazottak azonosítóinak listája valószínűleg 1 MB-nál nagyobb méretű, használja az 1. lehetőséget, és tárolja az indexet a blob Storage-ban.  
* Ha a 2. lehetőséget használja (az EGTs használatával kezeli az alkalmazottak hozzáadását és törlését, valamint az alkalmazott vezetéknevének módosítását), akkor ki kell értékelnie, hogy a tranzakciók mennyisége megközelíti-e egy adott partíció skálázhatósági korlátait. Ebben az esetben érdemes megfontolni egy végül konzisztens megoldást (1. vagy 3. lehetőség). Ezek a várólisták segítségével kezelik a frissítési kérelmeket, és lehetővé teszik, hogy az indexelt entitásokat külön partícióban tárolja az alkalmazott entitásokból.  
* A megoldás 2. beállítása feltételezi, hogy a vezetéknevét egy részlegen belül szeretné megkeresni. Tegyük fel például, hogy az értékesítési részleg vezetékneve alapján szeretné lekérni az alkalmazottak listáját. Ha azt szeretné, hogy a teljes szervezeten belül az összes alkalmazott vezetékneve legyen Jones, használja az 1. vagy a 3. lehetőséget.
* Olyan üzenetsor-alapú megoldást is alkalmazhat, amely végleges konzisztenciát biztosít. További részletekért tekintse meg a [végül konzisztens tranzakciók mintáját](#eventually-consistent-transactions-pattern).  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Akkor használja ezt a mintát, ha olyan entitásokat szeretne keresni, amelyek mindegyike közös tulajdonságot használ, például a vezetéknevét az utolsó nevű alkalmazottal.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Összetett kulcs mintája](#compound-key-pattern)  
* [Végül konzisztens tranzakciós minta](#eventually-consistent-transactions-pattern)  
* [Entitás-csoport tranzakciói](#entity-group-transactions)  
* [Heterogén entitások típusának használata](#work-with-heterogeneous-entity-types)  

### <a name="denormalization-pattern"></a>Denormalizálás mintája
A kapcsolódó adategységeket egyetlen entitásban egyesítheti, így egyetlen pont lekérdezéssel kérheti le az összes szükséges adat lekérését.  

#### <a name="context-and-problem"></a>Kontextus és probléma
A viszonyítási adatbázisban általában az adatok normalizálása történik, hogy eltávolítsa az ismétlődéseket, amikor a lekérdezések több táblából kérik le az adatok lekérdezését. Ha az adatait az Azure-táblákban normalizálja, akkor a kapcsolódó adatok lekérése érdekében az ügyféltől több oda-vissza kell, hogy beolvassa a kiszolgálót. A következő táblázatos struktúra esetében például két oda-és visszaút szükséges a részleg részleteinek lekéréséhez. Az egyik út lekéri a kezelő entitást, amely tartalmazza a felettes AZONOSÍTÓját, a második pedig beolvassa a felettes adatait egy alkalmazott entitásban.  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE16.png" alt-text="Részleg entitás és alkalmazott entitás ábrája":::

#### <a name="solution"></a>Megoldás
Ahelyett, hogy két különálló entitásban tárolja az adatokat, denormalizálja az adatokat, és megtartja a felettes adatainak másolatát a részleg entitásban. Például:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE17.png" alt-text="A denormalizált és a kombinált részleg entitásának ábrája":::

Az ezekkel a tulajdonságokkal rendelkező részleg entitásokkal mostantól lekérheti az adott részlegre vonatkozó összes adatot egy pont lekérdezés használatával.  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Az adattárolással kapcsolatos költségek kétszer vannak tárolva. A Table Storage-hoz kevesebb kérelemből eredő teljesítménybeli haszon általában a tárolási költségek marginális növekedését méri. Ezen túlmenően a költségeket részben ellensúlyozza a részleg részleteinek beolvasásához szükséges tranzakciók számának csökkentése.  
* Meg kell őriznie a két entitás konzisztenciáját, amelyek adatokat tárolnak a kezelők számára. A konzisztencia-problémát a EGTs használatával kezelheti egyetlen atomi tranzakcióban több entitás frissítéséhez. Ebben az esetben a részleg entitás és a Department Manager alkalmazott entitása ugyanabban a partícióban van tárolva.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Akkor használja ezt a mintát, ha gyakran kell kapcsolódó információkat keresnie. Ez a minta csökkenti azoknak a lekérdezéseknek a számát, amelyeket az ügyfélnek el kell végeznie a szükséges adatforrások lekéréséhez.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Összetett kulcs mintája](#compound-key-pattern)  
* [Entitás-csoport tranzakciói](#entity-group-transactions)  
* [Heterogén entitások típusának használata](#work-with-heterogeneous-entity-types)

### <a name="compound-key-pattern"></a>Összetett kulcs mintája
Összetett `RowKey` értékek használatával lehetővé teheti, hogy az ügyfél egyetlen pont lekérdezéssel keressen kapcsolódó adatokkal.  

#### <a name="context-and-problem"></a>Kontextus és probléma
A kapcsolati adatbázisban természetes, hogy a lekérdezésekben a kapcsolódó adatoknak az ügyfélhez való visszaküldéséhez egyetlen lekérdezésben használja a rendszer. Az alkalmazotti AZONOSÍTÓval például megkeresheti a kapcsolódó entitások listáját, amelyek az adott alkalmazott teljesítmény-és felülvizsgálati adatait tartalmazzák.  

Tegyük fel, hogy az Employee entitásokat a következő struktúra használatával tárolja a Table Storage-ban:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE18.png" alt-text="Az alkalmazotti entitás struktúrájának ábrája, amellyel az alkalmazotti entitásokat a Table Storage tárolja.":::

Emellett a korábbi adatokat is tárolnia kell a felülvizsgálatokhoz és a teljesítményhez a munkahelye által a szervezete számára készített munkavégzéshez kapcsolódóan, és az adatokat évente kell elérni. Az egyik lehetőség egy másik tábla létrehozása, amely az entitásokat az alábbi struktúrával tárolja:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE19.png" alt-text="Az alkalmazottak felülvizsgálati entitásának ábrája":::

Ezzel a módszerrel az új entitásban dönthet úgy, hogy az adatokat (például utónév és vezetéknév) duplikálja, hogy lehetővé tegye az adatok egyetlen kérelemkel való lekérését. Azonban nem lehet erős konzisztenciát fenntartani, mert nem használhat EGT a két entitás atomi frissítésére.  

#### <a name="solution"></a>Megoldás
Egy új entitás típusának tárolása az eredeti táblában az alábbi struktúrával rendelkező entitások használatával:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE20.png" alt-text="Az összetett kulccsal rendelkező alkalmazott entitás ábrája":::

Figyelje meg, hogy a `RowKey` mostantól egy összetett kulcs, amely az alkalmazotti azonosítóból és a felülvizsgálati adatmennyiségből áll. Ez lehetővé teszi az alkalmazottak teljesítményének beolvasását és az adatellenőrzést egyetlen, egyetlen entitásra vonatkozó kéréssel.  

Az alábbi példa azt ismerteti, hogyan kérhető le egy adott alkalmazott összes felülvizsgálati adata (például 000123 alkalmazott az értékesítési részlegben):  

$filter = (PartitionKey EQ "Sales") és (RowKey GE "empid_000123") és (RowKey lt "empid_000124") &$select = RowKey, felettes minősítés, társ-minősítés, megjegyzések  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Használjon egy megfelelő elválasztó karaktert, amely megkönnyíti az érték elemzését `RowKey` : például **000123_2012**.  
* Ezt az entitást ugyanabban a partícióban tárolja, mint a többi olyan entitást, amely ugyanahhoz az alkalmazotthoz kapcsolódó adatforrásokat tartalmaz. Ez azt jelenti, hogy a EGTs-t használhatja az erős konzisztencia fenntartásához.
* Érdemes megfontolni, hogy milyen gyakran kérdezi le az adatlekérdezést, hogy a minta megfelelő-e. Ha például ritkábban éri el az áttekintő adatvizsgálatot, és a fő alkalmazotti adatai gyakran, külön entitásként kell megtartani őket.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Akkor használja ezt a mintát, ha egy vagy több, gyakran lekérdezett kapcsolódó entitást kell tárolnia.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Entitás-csoport tranzakciói](#entity-group-transactions)  
* [Heterogén entitások típusának használata](#work-with-heterogeneous-entity-types)  
* [Végül konzisztens tranzakciós minta](#eventually-consistent-transactions-pattern)  

### <a name="log-tail-pattern"></a>Napló farok mintája
A partícióhoz legutóbb hozzáadott *n* entitások beolvasása egy olyan `RowKey` értékkel, amely fordított dátum és idő sorrendbe rendezi a sort.  

> [!NOTE]
> A Azure Cosmos DB Azure Table API által visszaadott lekérdezési eredmények nem a partíciós kulcs vagy a sor kulcsa szerint vannak rendezve. Így, míg ez a minta alkalmas a Table Storage-hoz, nem alkalmas Azure Cosmos DBra. A szolgáltatások közötti különbségek részletes listáját a [Azure Cosmos db és az Azure Table Storage Table API közötti különbségek](table-api-faq.md#table-api-vs-table-storage)című részben tekintheti meg.

#### <a name="context-and-problem"></a>Kontextus és probléma
Gyakori követelmény, hogy a legutóbb létrehozott entitásokat, például az alkalmazott által küldött tíz legutóbbi költséget kéri le. A tábla lekérdezései támogatják a `$top` lekérdezési műveletet, hogy a rendszer az első *n* entitást adja vissza egy készletből. Nincs egyenértékű lekérdezési művelet, amely egy készlet utolsó *n* entitását adja vissza.  

#### <a name="solution"></a>Megoldás
Az entitásokat egy, a `RowKey` természetes rendezéssel megfordított dátum/idő sorrendbe rendezve tárolja, így a legújabb bejegyzés mindig az első a táblában.  

Ha például le szeretné kérni az alkalmazott által küldött tíz legutóbbi költséget, az aktuális dátum/idő értékből származtatott fordított osztásjelek értékét is használhatja. A következő C#-kódrészlet az egyik módszert mutatja be, amely egy megfelelő "fordított kullancs" értéket hoz létre egy `RowKey` adott rendezéshez a legutóbbitől a legrégebbiig:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

A dátum/idő értékre a következő kóddal térhet vissza:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

A tábla lekérdezése a következőképpen néz ki:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* A fordított osztásjelek értékét a bevezető nullákkal kell ellátni, hogy a karakterlánc-érték a várt módon legyen rendezve.  
* A méretezhetőségi célokat a partíció szintjén kell figyelembe venni. Ügyeljen arra, hogy ne hozzon létre a gyors elérésű direktszínű partíciókat.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Akkor használja ezt a mintát, ha fordított dátum/idő sorrendben szeretné elérni az entitásokat, vagy ha a legutóbb hozzáadott entitásokhoz kell hozzáférnie.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Előtag/Hozzáfűzés Anti-pattern](#prepend-append-anti-pattern)  
* [Entitások beolvasása](#retrieve-entities)  

### <a name="high-volume-delete-pattern"></a>Nagy mennyiségű törlési minta
Engedélyezze a nagy mennyiségű entitás törlését úgy, hogy az összes entitást egyidejű törlésre tárolja a saját külön táblájában. Az entitásokat a tábla törlésével törölheti.  

#### <a name="context-and-problem"></a>Kontextus és probléma
Számos alkalmazás törli azokat a régi adatmennyiségeket, amelyek már nem szükségesek az ügyfélalkalmazás számára, vagy hogy az alkalmazás archiválva lett egy másik tárolóeszközre. Ezeket az adatok általában dátum alapján azonosíthatók. Tegyük fel például, hogy az összes olyan bejelentkezési kérelem rekordját törölni kell, amely több mint 60 napnál régebbi.  

Az egyik lehetséges kialakítás a bejelentkezési kérés dátumának és időpontjának használata a következőben `RowKey` :  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE21.png" alt-text="Bejelentkezési kísérlet entitásának ábrája":::

Ezzel a módszerrel elkerülhetők a partíciós hozzáférési pontok, mivel az alkalmazás egy külön partícióban beszúrhatja és törölheti a bejelentkezési entitásokat az egyes felhasználók számára. Ez a megközelítés azonban költséges és időigényes lehet, ha nagy számú entitással rendelkezik. Először be kell állítania egy táblázatos vizsgálatot a törölni kívánt entitások azonosításához, majd minden régi entitást törölnie kell. A régi entitások törléséhez a EGTs-ba történő több törlési kérelem kötegelt feldolgozásával csökkentheti a lekerekítési utak számát a kiszolgálóra.  

#### <a name="solution"></a>Megoldás
A bejelentkezési kísérletek minden napján külön táblázatot használjon. Az előző entitás kialakításával elkerülheti, hogy az entitások beszúrásakor ne legyenek elérhetők a pontok. A régi entitások törlése mostantól csupán egy olyan kérdés, hogy naponta törölni kell egy táblázatot (egyetlen tárolási művelet), ahelyett, hogy naponta több száz és ezer egyéni bejelentkezési entitást kellene megkeresnie és törölni.  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Az Ön által használt kialakítás más módon támogatja az adatokat, például bizonyos entitások keresését, más adatokkal való összekapcsolást vagy összesített információk létrehozását?  
* Az új entitások beszúrásakor a terv elkerüli a gyors helyeket?  
* Késleltetés, ha a törlés után ugyanazt a táblanév-nevet szeretné használni. Jobb, ha mindig egyedi táblanév-nevet használ.  
* Az új tábla első használatakor a Table Storage a hozzáférési mintákat is megtudhatja, és elosztja a partíciókat a csomópontok között. Érdemes megfontolni, hogy milyen gyakran kell új táblákat létrehoznia.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Akkor használja ezt a mintát, ha nagy mennyiségű entitást kell törölni egyszerre.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Entitás-csoport tranzakciói](#entity-group-transactions)
* [Entitások módosítása](#modify-entities)  

### <a name="data-series-pattern"></a>Adatsorozat mintája
Egyetlen entitásban tárolhatja a teljes adatsorozatokat, így csökkentve a kérelmek számát.  

#### <a name="context-and-problem"></a>Kontextus és probléma
Gyakori forgatókönyv, hogy egy alkalmazás egy adatsorozatot tárol, amelyet általában egyszerre kell lekérnie. Előfordulhat például, hogy az alkalmazás rögzíti, hogy az egyes alkalmazottak hány ÜZENETKÜLDÉSi üzenetet küldenek óránként, majd ezt az információt felhasználva ábrázolják, hogy az egyes felhasználók hány üzenetet küldenek az előző 24 órában. Az egyik kialakítás lehet 24 entitás tárolása az egyes alkalmazottak számára:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE22.png" alt-text="Az üzenet-stats entitás ábrája":::

Ezzel a kialakítással könnyedén megkeresheti és frissítheti az entitásokat, amelyeket frissíteni kell az egyes alkalmazottak számára, amikor az alkalmazásnak frissítenie kell az üzenetek számának értékét. Ahhoz azonban, hogy lekérje a tevékenység diagramjának az előző 24 órában való ábrázolásához szükséges információkat, 24 entitást kell lekérnie.  

#### <a name="solution"></a>Megoldás
A következő kialakítással külön tulajdonsággal tárolhatja az üzenetek darabszámát minden órában:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE23.png" alt-text="Az üzenet-stats entitást az elválasztott tulajdonságokkal ábrázoló ábra":::

Ezzel a kialakítással egy adott órában egy alkalmazotthoz tartozó üzenetek számának frissítése egyesítési művelettel végezhető el. Most lekérheti az összes szükséges információt, hogy a diagramot egyetlen entitásra vonatkozó kérelem használatával ábrázolja.  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Ha a teljes adatsorozat egyetlen entitáshoz sem fér hozzá (egy entitás legfeljebb 252 tulajdonsággal rendelkezhet), használjon másik adattárat, például egy blobot.  
* Ha több ügyfél is frissít egyszerre egy entitást, használja a **ETAG** az optimista Egyidejűség megvalósításához. Ha sok ügyfele van, érdemes lehet magas szintű elégedettséget tapasztalni.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Akkor használja ezt a mintát, ha egy adott entitáshoz társított adatsorozatot kell frissítenie és beolvasnia.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Nagyméretű entitások mintája](#large-entities-pattern)  
* [Egyesítés vagy csere](#merge-or-replace)  
* [Végül konzisztens tranzakciós minta](#eventually-consistent-transactions-pattern) (ha az adatsorozatot egy blobban tárolja)  

### <a name="wide-entities-pattern"></a>Széles entitások mintája
Több mint 252 tulajdonságú logikai entitások tárolására több fizikai entitást használjon.  

#### <a name="context-and-problem"></a>Kontextus és probléma
Az egyes entitások legfeljebb 252 tulajdonsággal rendelkezhetnek (kivéve a kötelező rendszertulajdonságokat), és összesen legfeljebb 1 MB adatmennyiséget tárolhatnak. A relációs adatbázisokban általában a sorok méretének korlátozásai is megtalálhatók egy új tábla hozzáadásával, valamint egy 1 – 1 közötti kapcsolat kényszerítésével.  

#### <a name="solution"></a>Megoldás
A Table Storage használatával több entitást is tárolhat, amelyek több mint 252 tulajdonsággal rendelkező, egyetlen nagy üzleti objektumot képviselnek. Ha például az elmúlt 365 napban az egyes alkalmazottak által küldött IM-üzenetek számának számát szeretné tárolni, akkor a következő kialakítást használhatja, amely két, különböző sémákkal rendelkező entitást használ:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE24.png" alt-text="Az Rowkey 01 és az Rowkey 02 üzenet-stats entitást tartalmazó üzenet-stats entitás ábrázolása":::

Ha olyan módosítást kell végeznie, amelyhez mindkét entitást frissíteni kell, hogy azok szinkronizálva maradjanak egymással, használhat egy EGT. Ellenkező esetben egyetlen egyesítési művelettel frissítheti az üzenetek darabszámát egy adott napra vonatkozóan. Egy adott alkalmazott összes értékének lekéréséhez mindkét entitást le kell kérnie. Ezt két, a és a értéket is használó, hatékony kéréssel teheti meg `PartitionKey` `RowKey` .  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósításának eldöntése során vegye figyelembe a következő pontot:  

* A teljes logikai entitások beolvasása legalább két tárolási tranzakciót foglal magában: egyet az egyes fizikai entitások lekéréséhez.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Akkor használja ezt a mintát, ha olyan entitásokat kell tárolnia, amelyek mérete vagy tulajdonságai meghaladják a Table Storage egyes entitásának korlátait.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Entitás-csoport tranzakciói](#entity-group-transactions)
* [Egyesítés vagy csere](#merge-or-replace)

### <a name="large-entities-pattern"></a>Nagyméretű entitások mintája
Nagyméretű tulajdonságértékek tárolására használjon blob Storage-t.  

#### <a name="context-and-problem"></a>Kontextus és probléma
Az egyes entitások összesen legfeljebb 1 MB adatmennyiséget tárolhatnak. Ha a tulajdonságok közül egy vagy több olyan értéket tárol, amely az entitás teljes méretét eredményezi az adott érték túllépésekor, a teljes entitás nem tárolható a Table Storage-ban.  

#### <a name="solution"></a>Megoldás
Ha az entitás mérete meghaladja az 1 MB-ot, mert egy vagy több tulajdonság nagy mennyiségű adatmennyiséget tartalmaz, akkor a blob Storage-ban tárolhatja az adatait, majd az entitás egyik tulajdonságában tárolhatja a blob címeit. Tárolhat például egy alkalmazott fényképét a blob Storage-ban, és tárolhat egy hivatkozást a fényképre az `Photo` alkalmazotti entitás tulajdonságában:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE25.png" alt-text="A blob Storage-ra mutató, az alkalmazotti entitást és a sztringet bemutató ábra":::

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* A táblázatos tárolóban lévő entitás és a blob Storage-beli adattárolók közötti végleges konzisztencia fenntartása érdekében a [végül konzisztens tranzakciós mintázattal](#eventually-consistent-transactions-pattern) kezelheti az entitásokat.
* A teljes entitások beolvasása legalább két tárolási tranzakciót foglal magában: egyet az entitás lekéréséhez, egyet pedig a blob adatainak lekéréséhez.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Akkor használja ezt a mintát, ha olyan entitásokat kell tárolnia, amelyek mérete meghaladja a Table Storage egyes entitásának korlátait.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Végül konzisztens tranzakciós minta](#eventually-consistent-transactions-pattern)  
* [Széles entitások mintája](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

### <a name="prependappend-anti-pattern"></a>Előtag/Hozzáfűzés Anti-pattern
Ha nagy mennyiségű lapkákkal rendelkezik, növelje a méretezhetőséget a lapkák több partíción való elosztásával.  

#### <a name="context-and-problem"></a>Kontextus és probléma
Az entitások a tárolt entitások számára történő előállítása vagy hozzáfűzése általában azt eredményezi, hogy az alkalmazás új entitásokat ad hozzá a partíciók egy sorozatából lévő első vagy utolsó partícióhoz. Ebben az esetben a lapkák mindegyike adott időpontban zajlik ugyanabban a partícióban, és létrehoz egy hotspotot. Ez megakadályozza, hogy a tábla tárterülete több csomóponton helyezzen el terheléselosztási lapkákat, és lehetséges, hogy az alkalmazás megnyomja a partíció skálázhatósági céljait. Vegyünk például egy olyan alkalmazás esetét, amely naplózza az alkalmazottak hálózati és erőforrás-hozzáférését. Az entitások szerkezete, például az alábbiak miatt az aktuális óra partíciója válik elérhetővé, ha a tranzakciók mennyisége eléri az egyes partíciók skálázhatósági célját:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE26.png" alt-text="Egy entitás-struktúra ábrája, amely az aktuális óra partíciójának elérését eredményezheti, ha a tranzakciók mennyisége eléri a méretezhetőségi célt egy adott partíció esetében.":::

#### <a name="solution"></a>Megoldás
A következő alternatív entitás-struktúra elkerüli a hotspotot egy adott partíción, mivel az alkalmazás naplózza az eseményeket:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE27.png" alt-text="Az alkalmazotti entitást bemutató ábra az év, hónap, nap, óra és eseményazonosító összetételével RowKey":::

Figyelje meg, hogy a és az `PartitionKey` `RowKey` összetett kulcsok is szerepelnek a példában. A a `PartitionKey` részleg és az alkalmazott azonosítója alapján osztja el a naplózást több partíció között.  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Az alternatív kulcs szerkezete, amely elkerüli a gyakori partíciók gyors létrehozását a lapkákon, hatékonyan támogatja az ügyfélalkalmazás által nyújtott lekérdezéseket?  
* Az Ön várható tranzakciós mennyisége azt jelenti, hogy valószínűleg el fogja érni az egyes partíciók méretezhetőségi céljait, és a Table Storage szabályozza azt?  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Ha a tranzakciós kötetek mennyisége valószínűleg a nagy mennyiségű partíció elérésekor a táblázatos tárterületet fogja eredményezni, kerülje a küldési/hozzáfűzési mintázatot.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Összetett kulcs mintája](#compound-key-pattern)  
* [Napló farok mintája](#log-tail-pattern)  
* [Entitások módosítása](#modify-entities)  

### <a name="log-data-anti-pattern"></a>Adatnapló adattípusa
A naplófájlok tárolásához általában blob Storage-t kell használnia a Table Storage helyett.  

#### <a name="context-and-problem"></a>Kontextus és probléma
A naplózási adatok általános használati esete egy adott dátum-/időtartományhoz tartozó naplóbejegyzések beolvasása. Megkeresheti például az összes olyan hibát és kritikus üzenetet, amelyet az alkalmazás a 15:04 és a 15:06 között adott dátumra naplóz. Nem kívánja használni a naplófájl dátumát és időpontját, hogy meghatározza azt a partíciót, amelybe a napló entitásokat menteni kívánja. Ez a művelet egy gyors partíciót eredményez, mivel az összes naplózási entitás ugyanazt az értéket fogja megosztani `PartitionKey` (lásd a következőt: [fűzni/append Anti-pattern](#prepend-append-anti-pattern)). A naplófájl következő entitás-sémája például egy gyors partíciót eredményez, mivel az alkalmazás az összes naplózási üzenetet az aktuális dátumra és órára írja a partícióra:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE28.png" alt-text="A naplózási üzenet entitás-sémáját megjelenítő ábra egy gyors partíciót eredményez.":::

Ebben a példában a a `RowKey` napló üzenetének dátumát és időpontját tartalmazza annak biztosítására, hogy a naplóüzenetek dátum/idő sorrendben legyenek rendezve. A `RowKey` emellett egy üzenet azonosítóját is tartalmazza abban az esetben, ha több naplófájl is ugyanazt a dátumot és időpontot használja.  

Egy másik módszer az a használata, `PartitionKey` amely gondoskodik arról, hogy az alkalmazás üzeneteket írjon a különböző partíciók között. Ha például a naplófájl forrása lehetővé teszi az üzenetek több partíción keresztüli terjesztését, használhatja a következő entitás-sémát:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE29.png" alt-text="A naplózási üzenet entitásának ábrája":::

A séma problémája azonban az, hogy egy adott időtartomány összes naplófájljának lekéréséhez a tábla minden partícióján keresni kell.

#### <a name="solution"></a>Megoldás
Az előző szakasz rámutatott arra a problémára, hogy a Table Storage-t a naplóbejegyzések tárolására próbálja használni, és két nem megfelelő kialakítást javasolt. Az egyik megoldás egy olyan gyors partíciót eredményezett, amely gyenge teljesítményű írási napló üzeneteinek kockázatával jár. A másik megoldás rossz lekérdezési teljesítményt eredményezett, mert a táblázatban lévő összes partíció vizsgálatára vonatkozó követelmény egy adott időtartományhoz tartozó naplófájlok beolvasásához szükséges. A blob Storage jobb megoldást kínál az ilyen típusú forgatókönyvek esetében, így az Azure Storage Analytics az általa gyűjtött adatokat tárolja.  

Ez a szakasz azt ismerteti, hogyan tárolja a Storage Analytics a naplózási adattárakat a blob Storage-ban, ennek a megközelítésnek az szemlélteti, hogy az adattárolást általában a tartomány alapján kérdezi le.  

A Storage Analytics több blobban is tagolt formátumban tárolja a naplózási üzeneteket. A tagolt formátum megkönnyíti az ügyfélalkalmazás számára a naplófájlban lévő adatelemzést.  

A Storage Analytics elnevezési konvenciója olyan blobokat használ, amelyekkel megkeresheti a keresett naplófájlokat tartalmazó blobot (vagy blobokat). Egy "üzenetsor/2014/07/31/1800/000001. log" nevű blob például a üzenetsor-szolgáltatáshoz kapcsolódó naplófájlokat tartalmaz, amelyek az óra 18:00-től kezdődően, 2014. július 31-ig érvényesek. A "000001" azt jelzi, hogy ez az időszak első naplófájlja. A Storage Analytics a blob metaadatainak részeként rögzíti a fájlban tárolt első és utolsó naplózási üzenetek időbélyegét is. A blob Storage-hoz készült API lehetővé teszi, hogy megkeresse a tárolóban lévő blobokat egy név előtagja alapján. Ha meg szeretné keresni az összes olyan blobot, amely a várakozási sor naplózási adatsorát tartalmazza, a következő előtaggal 18:00: "üzenetsor/2014/07/31/1800".  

A Storage Analytics pufferek belső naplófájlba helyezi az üzeneteket, majd rendszeresen frissíti a megfelelő blobot, vagy létrehoz egy újat a naplóbejegyzések legújabb kötegével. Ez csökkenti a blob Storage-hoz szükséges írási műveletek számát.  

Ha a saját alkalmazásában hasonló megoldást valósít meg, gondolja át, hogyan kezelheti a megbízhatóság és a költséghatékonyság, valamint a méretezhetőség közötti kompromisszumot. Más szóval értékelje ki, hogy milyen hatással van az összes naplóbejegyzés a blob Storage-ba való írására az alkalmazásban lévő frissítések pufferelése és a blob Storage-ba kötegekben való írása során.  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A naplófájlok tárolási módjának meghatározásakor vegye figyelembe a következő szempontokat:  

* Ha olyan táblázatot hoz létre, amely elkerüli a lehetséges gyors partíciókat, akkor előfordulhat, hogy nem fér hozzá a napló adataihoz.  
* A naplófájlok feldolgozásához az ügyfeleknek gyakran sok rekordot kell betölteni.  
* Bár a naplózási adatként gyakran strukturált, a blob Storage lehet jobb megoldás.  

### <a name="implementation-considerations"></a>Implementálási szempontok
Ez a szakasz azokat a szempontokat ismerteti, amelyeket figyelembe kell venni az előző szakaszokban leírt mintázatok megvalósításakor. Ennek a szakasznak a nagy része C# nyelven írt példákat használ, amelyek a Storage ügyféloldali kódtárat használják (az írás időpontjában a 4.3.0 verziója).  

### <a name="retrieve-entities"></a>Entitások beolvasása
Ahogy az a [lekérdezési](#design-for-querying)szakaszban is látható, a leghatékonyabb lekérdezés a pont lekérdezése. Bizonyos esetekben azonban előfordulhat, hogy több entitást kell lekérnie. Ez a szakasz néhány gyakori megközelítést ismertet az entitások a Storage ügyféloldali kódtár használatával történő beolvasásához.  

#### <a name="run-a-point-query-by-using-the-storage-client-library"></a>Pont lekérdezésének futtatása a Storage ügyféloldali kódtár használatával
A pontok lekérdezésének legegyszerűbb módja a tábla **lekérése** művelet használata. Ahogy az a következő C# kódrészletben is látható, a művelet lekérdezi az `PartitionKey` "értékesítés" értékkel rendelkező entitást és a `RowKey` "212" értéket:  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Figyelje meg, hogy ez a példa arra vár, hogy a beolvasott entitás típusa legyen `EmployeeEntity` .  

#### <a name="retrieve-multiple-entities-by-using-linq"></a>Több entitás beolvasása a LINQ használatával
A LINQ és a Storage ügyféloldali kódtár használatával több entitást is lekérhet, és egy **Where** záradékkal rendelkező lekérdezést is megadhat. A táblák vizsgálatának elkerüléséhez mindig adja meg a `PartitionKey` Where záradékban található értéket, és ha lehetséges, a `RowKey` tábla és a partíció vizsgálatának elkerüléséhez. A Table Storage támogatja az összehasonlító operátorok korlátozott készletét (nagyobb, mint, nagyobb vagy egyenlő, kevesebb, mint, kisebb vagy egyenlő, egyenlő és nem egyenlő) a WHERE záradékban való használathoz. A következő C# kódrészlet megkeresi az összes olyan alkalmazottat, akiknek a vezetékneve "B" karakterrel kezdődik (feltéve, hogy a `RowKey` vezetéknevet tárolja) az értékesítési részlegben (feltéve, hogy a `PartitionKey` részleg nevét tárolja):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
var employees = query.Execute();  
```

Figyelje meg, hogyan határozza meg a lekérdezés a `RowKey` és a `PartitionKey` értékeit a jobb teljesítmény érdekében.  

Az alábbi mintakód egyenértékű funkciókat mutat be a fluent API használatával (az általános Fluent API-kkal kapcsolatos további információkért lásd: [ajánlott eljárások Fluent API tervezéséhez](https://visualstudiomagazine.com/articles/2013/12/01/best-practices-for-designing-a-fluent-api.aspx)):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(
    TableQuery.CombineFilters(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition(
    "PartitionKey", QueryComparisons.Equal, "Sales"),
    TableOperators.And,
    TableQuery.GenerateFilterCondition(
    "RowKey", QueryComparisons.GreaterThanOrEqual, "B")
),
TableOperators.And,
TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "C")
    )
);
var employees = employeeTable.ExecuteQuery(employeeQuery);  
```

> [!NOTE]
> A minta több módszert ágyaz `CombineFilters` be a három szűrési feltétel belefoglalásához.  
> 
> 

#### <a name="retrieve-large-numbers-of-entities-from-a-query"></a>Nagy számú entitás beolvasása egy lekérdezésből
Az optimális lekérdezés egy egyedi entitást ad vissza egy `PartitionKey` érték és egy `RowKey` érték alapján. Bizonyos esetekben azonban előfordulhat, hogy az azonos partícióból, vagy akár sok partícióból származó entitások visszaadására van szükség. Az alkalmazás teljesítményét mindig teljes mértékben tesztelheti ilyen esetekben.  

A Table Storage-vel való lekérdezés egyszerre legfeljebb 1 000 entitást tud visszaadni, és legfeljebb öt másodpercig futhat. A Table Storage egy folytatási tokent ad vissza, amely lehetővé teszi, hogy az ügyfélalkalmazás a következő készletet kérje, ha a következők bármelyike igaz:

- Az eredményhalmaz több mint 1 000 entitást tartalmaz.
- A lekérdezés öt másodpercen belül nem fejeződött be.
- A lekérdezés átlépi a partíció határát. 

A folytatási tokenek működésével kapcsolatos további információkért lásd: [lekérdezési időkorlát és tördelés](/rest/api/storageservices/Query-Timeout-and-Pagination).  

Ha a Storage ügyféloldali kódtárat használja, akkor automatikusan képes kezelni a folytatási tokeneket, mert az entitásokat a Table Storage-ból adja vissza. A következő C# kód például automatikusan kezeli a folytatási jogkivonatokat, ha a Table Storage visszaadja őket a válaszban:  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

var employees = employeeTable.ExecuteQuery(employeeQuery);
foreach (var emp in employees)
{
        ...
}  
```

A következő C#-kód explicit módon kezeli a folytatási jogkivonatokat:  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

TableContinuationToken continuationToken = null;

do
{
        var employees = employeeTable.ExecuteQuerySegmented(
        employeeQuery, continuationToken);
    foreach (var emp in employees)
    {
    ...
    }
    continuationToken = employees.ContinuationToken;
} while (continuationToken != null);  
```

A folytatási tokenek explicit módon történő használatával szabályozhatja, hogy az alkalmazás Mikor kéri le a következő adatszegmenst. Ha például az ügyfélalkalmazás lehetővé teszi a felhasználók számára, hogy a táblázatban tárolt entitásokon keresztül Lapozzák fel a felhasználókat, a felhasználók dönthetnek úgy, hogy a lekérdezés által lekért összes entitáson át nem teszik a lapot. Az alkalmazás csak a folytatási tokent használja a következő szegmens lekéréséhez, amikor a felhasználó befejezte a lapozást az aktuális szegmens összes entitásán. Ez a megközelítés számos előnnyel jár:  

* Korlátozhatja a tábla tárterületéről lekérdezhető adatok mennyiségét, és áthelyezheti azokat a hálózaton keresztül.  
* Aszinkron I/O-t is végrehajthat a .NET-ben.  
* A folytatási tokent az állandó tárterületre is szerializálhatja, így az alkalmazás összeomlása esetén is folytathatja.  

> [!NOTE]
> A folytatási token általában egy 1 000 entitást tartalmazó szegmenst ad vissza, bár kevesebbet tartalmazhat. Ez abban az esetben is igaz, ha a lekérdezés által visszaadott bejegyzések számát korlátozza a  keresési feltételeknek megfelelő első n entitások visszaadásához. A Table Storage olyan szegmenst adhat vissza, amely kevesebb mint n entitást tartalmaz, valamint egy folytatási tokent, amely lehetővé teszi a fennmaradó entitások lekérését.  
> 
> 

A következő C#-kód azt mutatja be, hogyan lehet módosítani a szegmensen belül visszaadott entitások számát:  

```csharp
employeeQuery.TakeCount = 50;  
```

#### <a name="server-side-projection"></a>Kiszolgálóoldali kivetítés
Egyetlen entitás legfeljebb 255 tulajdonsággal rendelkezhet, és legfeljebb 1 MB méretű lehet. Ha lekérdezi a táblázatot, és beolvassa az entitásokat, előfordulhat, hogy nincs szüksége az összes tulajdonságra, és szükségtelenül nem tudja átvinni az adatátvitelt (a késés és a Cost csökkentése érdekében). Kiszolgálóoldali leképezés használatával csak a szükséges tulajdonságokat viheti át. A következő példa csak a tulajdonságot kérdezi le a `Email` `PartitionKey` `RowKey` `Timestamp` `ETag` lekérdezés által kiválasztott entitásokból (együtt:,, és).  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
List<string> columns = new List<string>() { "Email" };
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter).Select(columns);

var entities = employeeTable.ExecuteQuery(employeeQuery);
foreach (var e in entities)
{
        Console.WriteLine("RowKey: {0}, EmployeeEmail: {1}", e.RowKey, e.Email);
}  
```

Figyelje meg, hogy az `RowKey` érték Hogyan érhető el annak ellenére, hogy nem szerepel a lekérdezni kívánt Tulajdonságok listájában.  

### <a name="modify-entities"></a>Entitások módosítása
A Storage ügyféloldali kódtára lehetővé teszi, hogy az entitások beszúrásával, törlésével és frissítésével módosítsa a Table Storage-ban tárolt entitásokat. A EGTs használatával egyszerre több beszúrási, frissítési és törlési műveletet is használhat, így csökkentheti a szükséges lekerekítési utak számát, és javíthatja a megoldás teljesítményét.  

Kivételek, ha a Storage ügyféloldali kódtár egy EGT futtat, jellemzően a köteget okozó entitás indexét tartalmazza. Ez akkor hasznos, ha az EGTs-t használó kódot hibakeresést végez.  

Azt is gondolja át, hogy a terv hogyan befolyásolja az ügyfélalkalmazás a párhuzamossági és frissítési műveleteket.  

#### <a name="managing-concurrency"></a>Az egyidejűség kezelése
Alapértelmezés szerint a Table Storage egy optimista egyidejűségi ellenőrzést valósít meg az egyes entitások szintjén a beszúrási, egyesítési és törlési műveletekhez, bár lehetséges, hogy az ügyfél kényszeríti a Table Storage-t, hogy megkerüljék ezeket az ellenőrzéseket. További információ: [a Egyidejűség kezelése Microsoft Azure Storageban](../storage/blobs/concurrency-manage.md).  

#### <a name="merge-or-replace"></a>Egyesítés vagy csere
Az `Replace` osztály metódusa `TableOperation` mindig lecseréli a teljes entitást a Table Storage-ban. Ha nem tartalmaz tulajdonságot a kérelemben, ha az adott tulajdonság létezik a tárolt entitásban, a kérelem eltávolítja a tulajdonságot a tárolt entitásból. Hacsak nem szeretne explicit módon eltávolítani egy tulajdonságot egy tárolt entitásból, a kérelemben szereplő összes tulajdonságot fel kell vennie.  

Az `Merge` osztály metódusával `TableOperation` csökkentheti a táblázatos tárterületre küldött adatmennyiséget, ha egy entitást szeretne frissíteni. A `Merge` metódus lecseréli a tárolt entitásban lévő összes tulajdonságot a kérelemben szereplő entitás tulajdonságértékek értékével. Ez a metódus érintetlenül hagy minden olyan tulajdonságot, amely a kérelemben nem szereplő tárolt entitásban található. Ez akkor hasznos, ha nagyméretű entitásokkal rendelkezik, és csak kis számú tulajdonságot kell frissítenie egy kérelemben.  

> [!NOTE]
> A `*Replace` és a `Merge` metódus meghiúsul, ha az entitás nem létezik. Alternatív megoldásként használhatja a `InsertOrReplace` és a `InsertOrMerge` metódust is, amely új entitást hoz létre, ha az nem létezik.  
> 
> 

### <a name="work-with-heterogeneous-entity-types"></a>Heterogén entitások típusának használata
A Table Storage *séma nélküli tábla-* tároló. Ez azt jelenti, hogy egyetlen tábla több típusú entitást is tárolhat, ami nagy rugalmasságot biztosít a kialakításban. Az alábbi példa egy olyan táblázatot mutat be, amely az alkalmazottak és a részleg entitásait tárolja:  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Életkor</th>
<th>E-mail</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Életkor</th>
<th>E-mail</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Életkor</th>
<th>E-mail</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Minden entitásnak továbbra is rendelkeznie kell, és értékeket kell tartalmaznia, `PartitionKey` `RowKey` `Timestamp` de a tulajdonságok bármilyen készlete lehet. Emellett nem kell megjelölnie egy entitás típusát, ha nem úgy dönt, hogy valahol tárolja ezt az információt. Az entitás típusának azonosítására két lehetőség áll rendelkezésre:  

* Adja meg az entitás típusát a `RowKey` következőre: (vagy esetleg a `PartitionKey` ). Például `EMPLOYEE_000123` vagy `DEPARTMENT_SALES` `RowKey` értékként.  
* Az entitás típusát a következő táblázatban látható módon rögzítheti egy külön tulajdonsággal.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Életkor</th>
<th>E-mail</th>
</tr>
<tr>
<td>Alkalmazott</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Életkor</th>
<th>E-mail</th>
</tr>
<tr>
<td>Alkalmazott</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Részleg</td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Életkor</th>
<th>E-mail</th>
</tr>
<tr>
<td>Alkalmazott</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Az első lehetőség, amely az entitás típusától függően a értékre van kiválasztva, akkor hasznos lehet, `RowKey` Ha fennáll a lehetősége, hogy a különböző típusú entitások esetében azonos a kulcs értéke. A partícióban azonos típusú entitásokat is csoportosít.  

Az ebben a szakaszban tárgyalt technikák különösen az[öröklési kapcsolatokkal](#inheritance-relationships)kapcsolatos vitára vonatkoznak.  

> [!NOTE]
> Vegye figyelembe a verziószámot az entitás típusa értékben, hogy lehetővé váljon az ügyfélalkalmazások számára a POCO-objektumok fejlesztése és a különböző verziók használata.  
> 
> 

A szakasz további része a Storage ügyféloldali kódtár néhány olyan szolgáltatását ismerteti, amely megkönnyíti a több entitás típusának használatát ugyanabban a táblában.  

#### <a name="retrieve-heterogeneous-entity-types"></a>Heterogén entitások típusának beolvasása
Ha a Storage ügyféloldali kódtárat használja, három lehetősége van több entitás típusának használatára.  

Ha ismeri a megadott és az értékekkel tárolt entitás típusát `RowKey` `PartitionKey` , akkor megadhatja az entitás típusát az entitás beolvasása során. Ezt az előző két példában látta el, amely a következő típusú entitásokat kéri le `EmployeeEntity` : [egy pont lekérdezésének futtatása a Storage ügyféloldali kódtár használatával](#run-a-point-query-by-using-the-storage-client-library) , és [több entitás beolvasása a LINQ használatával](#retrieve-multiple-entities-by-using-linq).  

A második lehetőség, hogy a `DynamicTableEntity` típust (a tulajdonság táska) használja a konkrét poco típusú entitások típusának megadása helyett. Ez a beállítás javíthatja a teljesítményt is, mivel nem szükséges szerializálni és deszerializálni az entitást a .NET-típusokhoz. A következő C#-kód lehetséges, hogy több, különböző típusú entitást kér le a táblából, de az összes entitást `DynamicTableEntity` példányként adja vissza. Ezután a tulajdonság használatával `EntityType` határozza meg az egyes entitások típusát:  

```csharp
string filter = TableQuery.CombineFilters(
    TableQuery.GenerateFilterCondition("PartitionKey",
    QueryComparisons.Equal, "Sales"),
    TableOperators.And,
    TableQuery.CombineFilters(
    TableQuery.GenerateFilterCondition("RowKey",
                    QueryComparisons.GreaterThanOrEqual, "B"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey",
        QueryComparisons.LessThan, "F")
    )
);
TableQuery<DynamicTableEntity> entityQuery =
    new TableQuery<DynamicTableEntity>().Where(filter);
var employees = employeeTable.ExecuteQuery(entityQuery);

IEnumerable<DynamicTableEntity> entities = employeeTable.ExecuteQuery(entityQuery);
foreach (var e in entities)
{
EntityProperty entityTypeProperty;
if (e.Properties.TryGetValue("EntityType", out entityTypeProperty))
{
    if (entityTypeProperty.StringValue == "Employee")
    {
        // Use entityTypeProperty, RowKey, PartitionKey, Etag, and Timestamp
        }
    }
}  
```

Más tulajdonságok lekéréséhez a metódust kell használnia az `TryGetValue` `Properties` osztály tulajdonságán `DynamicTableEntity` .  

A harmadik lehetőség a `DynamicTableEntity` típus és egy példány együttes használata `EntityResolver` . Ez lehetővé teszi több POCO típus feloldását ugyanabban a lekérdezésben. Ebben a példában a `EntityResolver` delegált a `EntityType` tulajdonság használatával különbözteti meg a lekérdezés által visszaadott két típusú entitást. A `Resolve` metódus a `resolver` delegált használatával oldja fel a `DynamicTableEntity` példányokat a példányok között `TableEntity` .  

```csharp
EntityResolver<TableEntity> resolver = (pk, rk, ts, props, etag) =>
{

        TableEntity resolvedEntity = null;
        if (props["EntityType"].StringValue == "Department")
        {
        resolvedEntity = new DepartmentEntity();
        }
        else if (props["EntityType"].StringValue == "Employee")
        {
        resolvedEntity = new EmployeeEntity();
        }
        else throw new ArgumentException("Unrecognized entity", "props");

        resolvedEntity.PartitionKey = pk;
        resolvedEntity.RowKey = rk;
        resolvedEntity.Timestamp = ts;
        resolvedEntity.ETag = etag;
        resolvedEntity.ReadEntity(props, null);
        return resolvedEntity;
};

string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<DynamicTableEntity> entityQuery =
        new TableQuery<DynamicTableEntity>().Where(filter);

var entities = employeeTable.ExecuteQuery(entityQuery, resolver);
foreach (var e in entities)
{
        if (e is DepartmentEntity)
        {
    ...
        }
        if (e is EmployeeEntity)
        {
    ...
        }
}  
```

#### <a name="modify-heterogeneous-entity-types"></a>Heterogén entitások típusának módosítása
Nem kell tudnia, hogy milyen típusú entitást kell törölni, és mindig ismeri az entitás típusát az beszúrásakor. A típus használatával azonban a típusának `DynamicTableEntity` ismerete nélkül is frissítheti az entitásokat, és nem használhatja a Poco Entity osztályt. A következő mintakód egyetlen entitást kérdez le, és ellenőrzi, hogy a `EmployeeCount` tulajdonság létezik-e a frissítés előtt.  

```csharp
TableResult result =
        employeeTable.Execute(TableOperation.Retrieve(partitionKey, rowKey));
DynamicTableEntity department = (DynamicTableEntity)result.Result;

EntityProperty countProperty;

if (!department.Properties.TryGetValue("EmployeeCount", out countProperty))
{
        throw new
        InvalidOperationException("Invalid entity, EmployeeCount property not found.");
}
countProperty.Int32Value += 1;
employeeTable.Execute(TableOperation.Merge(department));  
```

### <a name="control-access-with-shared-access-signatures"></a>Hozzáférés vezérlése közös hozzáférésű aláírásokkal
A megosztott hozzáférés-aláírási (SAS-) tokenekkel engedélyezheti az ügyfélalkalmazások számára, hogy közvetlenül módosíthassák (és lekérdezési) a táblák entitásait anélkül, hogy közvetlenül a Table Storage szolgáltatással kellene hitelesíteni őket. Az SAS használatának három fő előnye van az alkalmazásban:  

* A Storage-fiók kulcsát nem szükséges egy nem biztonságos platformra (például egy mobileszközön) terjeszteni ahhoz, hogy az eszköz hozzáférjen és módosíthassa az entitásokat a Table Storage-ban.  
* A webes és feldolgozói szerepkörök egy részét kiszervezheti az entitások kezelése során. Kiszervezheti az ügyfelek eszközeit, például a végfelhasználói számítógépeket és a mobil eszközöket.  
* Korlátozott és időkorlátos engedélyeket rendelhet egy ügyfélhez (például engedélyezheti a csak olvasási hozzáférést adott erőforrásokhoz).  

További információ az SAS-tokenek Table Storage használatával történő használatáról: [közös hozzáférésű aláírások (SAS) használata](../storage/common/storage-sas-overview.md).  

Azonban továbbra is olyan SAS-jogkivonatokat kell megadnia, amelyek az ügyfélalkalmazások számára engedélyezik a Table Storage-beli entitásokat. Ezt olyan környezetben tegye meg, amely biztonságos hozzáférést biztosít a Storage-fiók kulcsaihoz. Általában webes vagy feldolgozói szerepkört használ az SAS-jogkivonatok létrehozásához, és azokat olyan ügyfélalkalmazások számára kézbesíteni, amelyeknek hozzá kell férniük az entitásokhoz. Mivel továbbra is az SAS-tokenek ügyfeleknek való létrehozásával és megvalósításával kapcsolatos költségek merülnek fel, érdemes megfontolni, hogy a lehető leghatékonyabban csökkentse a terhelést, különösen nagy mennyiségű forgatókönyv esetén.  

Létre lehet hozni egy SAS-jogkivonatot, amely hozzáférést biztosít a táblákban lévő entitások egy részhalmazához. Alapértelmezés szerint egy teljes táblához hoz létre SAS-jogkivonatot. Azt is megteheti, hogy az SAS-jogkivonat hozzáférést biztosít egy tartományhoz `PartitionKey` vagy egy tartományhoz vagy egy `PartitionKey` `RowKey` értékhez. Dönthet úgy is, hogy SAS-jogkivonatokat állít elő a rendszer egyes felhasználói számára, így az egyes felhasználók SAS-jogkivonata csak a saját entitásokhoz való hozzáférést teszi lehetővé a Table Storage szolgáltatásban.  

### <a name="asynchronous-and-parallel-operations"></a>Aszinkron és párhuzamos műveletek
Ha több partíción terjeszti át a kéréseket, az átviteli sebességet és az ügyfelek közötti rugalmasságot aszinkron vagy párhuzamos lekérdezések használatával növelheti.
Előfordulhat például, hogy két vagy több feldolgozói szerepkörrel rendelkező példánya párhuzamosan fér hozzá a táblákhoz. Egyéni feldolgozói szerepköröket is használhat a partíciók bizonyos csoportjai számára, vagy egyszerűen több feldolgozói szerepkör-példánnyal rendelkezhet, amelyek mindegyike egy tábla összes partícióját elérheti.  

Egy ügyfél-példányon belül a tárolási műveletek aszinkron futtatásával javíthatja az átviteli sebességet. A Storage ügyféloldali kódtára megkönnyíti az aszinkron lekérdezések és módosítások írását. Előfordulhat például, hogy a szinkron metódussal indul, amely egy partíció összes entitását lekéri, ahogy az a következő C#-kódban látható:  

```csharp
private static void ManyEntitiesQuery(CloudTable employeeTable, string department)
{
        string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

        TableContinuationToken continuationToken = null;

        do
        {
        var employees = employeeTable.ExecuteQuerySegmented(
                employeeQuery, continuationToken);
        foreach (var emp in employees)
    {
        ...
    }
        continuationToken = employees.ContinuationToken;
        } while (continuationToken != null);
}  
```

A kód egyszerűen módosítható úgy, hogy a lekérdezés aszinkron módon fusson, a következőképpen:  

```csharp
private static async Task ManyEntitiesQueryAsync(CloudTable employeeTable, string department)
{
        string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);
        TableContinuationToken continuationToken = null;

        do
        {
        var employees = await employeeTable.ExecuteQuerySegmentedAsync(
                employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            ...
        }
        continuationToken = employees.ContinuationToken;
            } while (continuationToken != null);
}  
```

Ebben az aszinkron példában a szinkron verzió következő változásai láthatók:  

* A metódus aláírása mostantól tartalmazza a `async` módosítót, és visszaad egy `Task` példányt.  
* Ahelyett, hogy meghívja a `ExecuteSegmented` metódust az eredmények lekéréséhez, a metódus most meghívja a `ExecuteSegmentedAsync` metódust. A metódus a `await` módosító használatával aszinkron módon kéri le az eredményeket.  

Az ügyfélalkalmazás többször is meghívhatja ezt a metódust, eltérő értékekkel a `department` paraméterhez. Mindegyik lekérdezés külön szálon fut.  

Az osztályban nem található a metódus aszinkron verziója `Execute` `TableQuery` , mert az `IEnumerable` illesztőfelület nem támogatja az aszinkron enumerálást.  

Az entitásokat aszinkron módon is beszúrhatja, frissítheti és törölheti. A következő C#-példa egy egyszerű, szinkron módszert mutat be egy alkalmazott entitás beszúrásához vagy cseréjéhez:  

```csharp
private static void SimpleEmployeeUpsert(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = employeeTable
        .Execute(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

A kód egyszerűen módosítható úgy, hogy a frissítés aszinkron módon fusson, a következőképpen:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = await employeeTable
        .ExecuteAsync(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Ebben az aszinkron példában a szinkron verzió következő változásai láthatók:  

* A metódus aláírása mostantól tartalmazza a `async` módosítót, és visszaad egy `Task` példányt.  
* Ahelyett, hogy meghívja a metódust az `Execute` entitás frissítésére, a metódus most meghívja a `ExecuteAsync` metódust. A metódus a `await` módosító használatával aszinkron módon kéri le az eredményeket.  

Az ügyfélalkalmazás több aszinkron metódust is meghívhat, így az egyes metódusok külön szálon futnak.
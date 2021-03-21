---
title: Azure Table Storage tervezése lekérdezésekhez | Microsoft Docs
description: Táblák megtervezése az Azure Table Storage-beli lekérdezésekhez. Válasszon ki egy megfelelő partíciót, optimalizálja a lekérdezéseket, és rendezze az adathalmazt a Table service számára.
services: storage
author: tamram
ms.author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.subservice: tables
ms.openlocfilehash: 43ae21d97bc9d8292270ae62006e649f4bcf540b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93316149"
---
# <a name="design-for-querying"></a>Tervezés lekérdezéshez
Table service megoldások intenzív, írásos vagy a kettő kombinációját is beolvashatja. Ez a cikk azokat a dolgokat ismerteti, amelyeket figyelembe kell vennie, amikor megtervezi a Table service az olvasási műveletek hatékony támogatásához. Az olvasási műveleteket hatékonyan támogató kialakítás általában írási műveletek esetén is hatékony. Azonban további szempontokat is figyelembe kell vennie az írási műveletek támogatásának tervezésekor, a cikk az [adatok módosítására szolgáló tervezési tervében](table-storage-design-for-modification.md)tárgyalt.

Egy jó kiindulási pont a Table service megoldás megtervezéséhez, amely lehetővé teszi az adatok hatékony olvasását, ha megkérdezi, hogy milyen lekérdezéseket kell végrehajtani az alkalmazásnak, hogy lekérje a szükséges adatok lekérését a Table service? "  

> [!NOTE]
> A Table service esetében fontos, hogy a kialakítás megtörténjen, mert nehéz és költséges lehet később módosítani. Például egy rokon adatbázisban gyakran lehetséges a teljesítménnyel kapcsolatos problémák megoldása, ha indexeket ad hozzá egy meglévő adatbázishoz: ez nem egy lehetőség a Table service.  
> 
> 

Ez a szakasz azokat a kulcsfontosságú problémákat tárgyalja, amelyeket a táblák lekérdezéshez való tervezésekor kell megadnia. Az ebben a szakaszban szereplő témakörök a következők:

* [A PartitionKey és a RowKey hatása a lekérdezési teljesítményre](#how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance)
* [Megfelelő PartitionKey kiválasztása](#choosing-an-appropriate-partitionkey)
* [A Table service lekérdezések optimalizálása](#optimizing-queries-for-the-table-service)
* [Adatrendezés a Table serviceban](#sorting-data-in-the-table-service)

## <a name="how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance"></a>A PartitionKey és a RowKey hatása a lekérdezési teljesítményre
A következő példák azt feltételezik, hogy a Table Service az alábbi struktúrával tárolja az alkalmazotti entitásokat (a legtöbb példa kihagyja az Érthetőségi **timestamp** tulajdonságot):  

| *Oszlop neve* | *Adattípus* |
| --- | --- |
| **PartitionKey** (részleg neve) |Sztring |
| **RowKey** (alkalmazott azonosítója) |Sztring |
| **FirstName** |Sztring |
| **LastName** |Sztring |
| **Életkor** |Egész szám |
| **EmailAddress** |Sztring |

Az [Azure Table Storage áttekintése című](table-storage-overview.md) cikk az Azure Table Service főbb funkcióit ismerteti, amelyek közvetlen hatással vannak a lekérdezés tervezésére. Ezek a következő általános irányelveket eredményezik Table service lekérdezések tervezéséhez. Vegye figyelembe, hogy az alábbi példákban használt szűrési szintaxis a Table service REST API, további információ: [lekérdezési entitások](/rest/api/storageservices/Query-Entities).  

* Egy ***pont lekérdezése** _ a leghatékonyabb keresési módszer, amelyet a legalacsonyabb késleltetést igénylő nagy mennyiségű keresésekhez vagy keresésekhez ajánlott használni. Egy ilyen lekérdezés az indexek segítségével rendkívül hatékonyan megkeresheti az egyes entitásokat az _ *PartitionKey** és a **RowKey** értékek megadásával. Például: $filter = (PartitionKey EQ ' Sales ') és (RowKey EQ ' 2 ')  
* A második legjobb a ***Range Query** _, amely az _ *PartitionKey**-t használja, és a szűrőket számos **RowKey** értékkel több entitást ad vissza. A **PartitionKey** érték egy adott partíciót azonosít, és a **RowKey** értékek a partícióban lévő entitások egy részhalmazát azonosítják. Például: $filter = PartitionKey EQ ' Sales ' and RowKey GE ' and RowKey lt 'T '  
* A harmadik legjobb a ***Partition Scan** _, amely a _ *PartitionKey** és szűrőket használja egy másik, nem kulcsfontosságú tulajdonságnál, amely több entitást is visszaadhat. A **PartitionKey** érték egy adott partíciót azonosít, és a tulajdonságértékek a partícióban lévő entitások egy részhalmaza számára kiválasztva. Például: $filter = PartitionKey EQ "Sales" és LastName EQ "Smith"  
* A ***Table Scan** _ nem tartalmazza a _ *PartitionKey** karaktert, és nagyon nem hatékony, mert megkeresi az összes olyan partíciót, amelyik felkészíti a táblázatot a megfelelő entitásokra. Táblázatos vizsgálatot végez, függetlenül attól, hogy a szűrő a **RowKey** használja-e. Például: $filter = LastName EQ ' Jones '  
* Azok a lekérdezések, amelyek több entitást adnak vissza, **PartitionKey** és **RowKey** sorrendben rendezik őket. Ha nem szeretné, hogy az entitások ne legyenek az ügyfélben, válasszon egy **RowKey** , amely meghatározza a leggyakoribb rendezési sorrendet.  

Vegye figyelembe, hogy a "**vagy**" használatával **RowKey** -értékek alapján határozhat meg egy partíciós vizsgálatot, és nem kezelhető tartomány-lekérdezésként. Ezért érdemes elkerülni a szűrőket használó lekérdezéseket, például a következőket: $filter = PartitionKey EQ ' Sales ' és (RowKey EQ ' 121 ' vagy RowKey EQ ' 322 ')  

A Storage ügyféloldali kódtárat használó ügyféloldali kódokra vonatkozó példákat a hatékony lekérdezések végrehajtásához lásd:  

* [Pont lekérdezés végrehajtása a Storage ügyféloldali kódtár használatával](table-storage-design-patterns.md#executing-a-point-query-using-the-storage-client-library)
* [Több entitás beolvasása a LINQ használatával](table-storage-design-patterns.md#retrieving-multiple-entities-using-linq)
* [Kiszolgálóoldali kivetítés](table-storage-design-patterns.md#server-side-projection)  

Az ugyanabban a táblában tárolt több entitást kezelő ügyféloldali kódokra vonatkozó Példákért lásd:  

* [Heterogén entitások típusának használata](table-storage-design-patterns.md#working-with-heterogeneous-entity-types)  

## <a name="choosing-an-appropriate-partitionkey"></a>Megfelelő PartitionKey kiválasztása
Az Ön által választott **PartitionKey** egyensúlyt kell biztosítania ahhoz, hogy lehetővé váljon a EGTs használata (az egységesség biztosítása érdekében) az entitások több partíción való terjesztésének követelménye (a méretezhető megoldás biztosításához).  

Egyetlen szélsőséges esetben egyetlen partícióban tárolhatja az összes entitást, ez azonban korlátozhatja a megoldás méretezhetőségét, és megakadályozhatja, hogy a Table Service terheléselosztási kérelmeket tudjon betölteni. A másik véglet, ha egy entitást tárol egy partíción, ami nagy mértékben méretezhető, és lehetővé teszi a Table szolgáltatás számára, hogy terheléselosztási kérelmeket helyezzen el, de ez megakadályozná az Entity Group-tranzakciók használatát.  

Az ideális **PartitionKey** , amely lehetővé teszi, hogy hatékony lekérdezéseket használjon, és elegendő partíciót biztosítson a megoldás méretezhetőségének biztosításához. Általában úgy látja, hogy az entitások megfelelő tulajdonsággal rendelkeznek, amely az entitásokat a megfelelő partíciók között osztja szét.

> [!NOTE]
> A felhasználókkal vagy alkalmazottakkal kapcsolatos adatokat tároló rendszerekben például a UserID jó PartitionKey lehet. Előfordulhat, hogy több olyan entitása van, amely egy adott felhasználóazonosító-t használ partíciós kulcsként. Minden olyan entitás, amely egy felhasználó adatait tárolja, egyetlen partícióba van csoportosítva, így ezek az entitások az Entity Group Transactions használatával érhetők el, miközben továbbra is jól méretezhetők.
> 
> 

További szempontokat is figyelembe kell vennie a **PartitionKey** , amelyek az entitások beszúrására, frissítésére és törlésére vonatkoznak. További információ: [táblák tervezése adatmódosításra](table-storage-design-for-modification.md).  

## <a name="optimizing-queries-for-the-table-service"></a>A Table service lekérdezések optimalizálása
A Table service automatikusan indexeli az entitásokat egy fürtözött indexben lévő **PartitionKey** és **RowKey** értékekkel, ezért az az oka, hogy a pontok lekérdezései a leghatékonyabbak. A **PartitionKey** és a **RowKey** fürtözött indexén azonban nincsenek indexek.

Számos tervnek meg kell felelnie az entitások több feltételen alapuló keresésének engedélyezéséhez szükséges követelményeknek. Például az alkalmazotti entitások az e-mailek, az alkalmazotti AZONOSÍTÓk vagy a vezetéknév alapján vannak megkeresve. A [táblázatos tervezési mintákban](table-storage-design-patterns.md) leírt mintázatok az ilyen típusú követelményekkel foglalkoznak, és leírják, hogy a Table Service milyen módszereket biztosít a másodlagos indexek eléréséhez:  

* [Partíción belüli másodlagos index minta](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) – az egyes entitások több példányának tárolása különböző **RowKey** -értékekkel (ugyanabban a partícióban) a gyors és hatékony keresési és alternatív rendezési sorrendek eltérő **RowKey** -értékek használatával történő engedélyezéséhez.  
* [Partíciók közötti másodlagos index minta](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) – az egyes entitások több példányának tárolása különböző **RowKey** -értékekkel külön partíciókban vagy külön táblákban, hogy a gyors és hatékony keresési és alternatív rendezési sorrendet a különböző **RowKey** értékek használatával engedélyezze.  
* [Entitások indexelése](table-storage-design-patterns.md#index-entities-pattern) – az indexelési entitások megtartása az entitások listáját visszaadó hatékony keresések engedélyezéséhez.  

## <a name="sorting-data-in-the-table-service"></a>Adatrendezés a Table serviceban
A Table service a **PartitionKey** , majd a **RowKey** alapján növekvő sorrendbe rendezi az entitásokat. Ezek a kulcsok karakterlánc-értékek, és így biztosítható, hogy a numerikus értékek megfelelően rendezettek legyenek, és nulla értékkel kell alakítani őket. Ha például a **RowKey** használt alkalmazotti azonosító érték egész érték, akkor a **123** -as alkalmazott azonosítóját a **00000123**-re kell konvertálnia.  

Számos alkalmazás rendelkezik a különböző megrendelésekben tárolt adatok használatára vonatkozó követelményekkel: például az alkalmazottak név szerinti rendezése vagy a csatlakozás dátuma. A következő minták az entitások alternatív rendezési sorrendjét ismertetik:  

* [Partíción belüli másodlagos index minta](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) – az egyes entitások több példányának tárolása különböző RowKey-értékekkel (ugyanabban a partícióban) a gyors és hatékony keresési és alternatív rendezési sorrendek eltérő RowKey-értékek használatával történő engedélyezéséhez.  
* [Partíciók közötti másodlagos index minta](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) – az egyes entitások több példányának tárolása különböző RowKey-értékekkel különálló partíciókban külön táblákban, amelyek lehetővé teszik a gyors és hatékony keresési és alternatív rendezési sorrendek használatát a különböző RowKey értékek használatával.
* [Naplóbeli farok minta](table-storage-design-patterns.md#log-tail-pattern) – a partícióhoz legutóbb hozzáadott *n* entitások beolvasása egy **RowKey** értékkel, amely fordított dátum és idő sorrendbe rendezi.  

## <a name="next-steps"></a>Következő lépések

- [Táblatervezési minták](table-storage-design-patterns.md)
- [Kapcsolatok modellezése](table-storage-design-modeling.md)
- [Tábla adatai titkosítása](table-storage-design-encrypt-data.md)
- [Tervezés adatmódosításhoz](table-storage-design-for-modification.md)
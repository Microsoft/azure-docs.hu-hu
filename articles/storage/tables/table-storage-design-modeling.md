---
title: Modellezési kapcsolatok az Azure Table Storage kialakításában | Microsoft Docs
description: Ismerje meg a modellezési folyamatot az Azure Table Storage-megoldás tervezésekor. További információ az egy-a-többhöz, egy az egyhez és az öröklési kapcsolatokról.
services: storage
ms.service: storage
author: tamram
ms.author: tamram
ms.topic: article
ms.date: 04/23/2018
ms.subservice: tables
ms.openlocfilehash: 3023b478ef7a4aaf6d9239e997bdf63282b56210
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88271192"
---
# <a name="modeling-relationships"></a>Kapcsolatok modellezése
Ez a cikk az Azure Table Storage-megoldások kialakításához segítséget nyújtó modellezési folyamatot ismerteti.

A tartományi modellek kiépítése kulcsfontosságú lépés a komplex rendszerek kialakításában. Általában a modellezési folyamattal azonosíthatja az entitásokat és a köztük lévő kapcsolatokat az üzleti tartomány megismerése érdekében, és tájékoztatja a rendszer kialakításáról. Ez a szakasz azt ismerteti, hogyan lehet lefordítani a tartományi modellekben található általános kapcsolattípus-típusokat a Table service kialakításához. A logikai adatmodellből fizikai NoSQL-alapú adatmodellre való hozzárendelési folyamat különbözik a kapcsolatok adatbázisának tervezésekor használt adatoktól. A rokon adatbázisok kialakítása általában feltételezi a redundancia minimalizálása érdekében optimalizált adatnormalizálás folyamatát, valamint egy deklaratív lekérdezési képességet, amely absztrakt módon ismerteti az adatbázis működésének megvalósítását.  

## <a name="one-to-many-relationships"></a>Egy-a-többhöz kapcsolat
Gyakran fordulnak elő egy-a-többhöz kapcsolat az üzleti tartomány objektumai között: például az egyik részleg számos alkalmazottal rendelkezik. Több módon is megvalósítható egy-a-többhöz kapcsolat a Table serviceban, amelyek az adott forgatókönyvhöz kapcsolódó előnyökkel és hátrányokkal rendelkeznek.  

Vegyünk példaként egy nagyméretű, többnemzetiségű vállalatot, amely több tízezer részleget és alkalmazotti entitást tartalmaz, ahol minden részlegnek számos alkalmazottja van, és mindegyik alkalmazott egy adott részleghez társítva van. Az egyik módszer a különálló részleg és az alkalmazotti entitások (például a következők) tárolása:  


![Különálló részleg és alkalmazott entitások tárolása](media/storage-table-design-guide/storage-table-design-IMAGE01.png)

Ez a példa egy implicit, egy-a-többhöz kapcsolatot mutat be a típusok között a **PartitionKey** érték alapján. Minden részlegnek számos alkalmazottja lehet.  

Ez a példa egy részleg entitást és a hozzá tartozó alkalmazott entitásokat is megjeleníti ugyanabban a partícióban. Dönthet úgy, hogy különböző partíciókat, táblákat vagy akár tárolási fiókokat is használhat a különböző entitások típusaihoz.  

Egy másik megoldás az, hogy denormalizálja az adatait, és csak az alkalmazott entitásokat tárolja a denormalizált részleg adataival az alábbi példában látható módon. Ebben az adott esetben ez a normalizált megközelítés nem a legjobb megoldás, ha a Department Manager részletes adatainak módosítására van szükség, mivel ehhez frissítenie kell a részleg összes alkalmazottját.  

![Alkalmazotti entitás](media/storage-table-design-guide/storage-table-design-IMAGE02.png)

További információ: a [denormalizálás mintája](table-storage-design-patterns.md#denormalization-pattern) az útmutató későbbi részében.  

Az alábbi táblázat összefoglalja a fentiekben ismertetett megközelítések előnyeit és hátrányait, amelyek egy-a-többhöz kapcsolattal rendelkező alkalmazotti és részlegi entitások tárolására szolgálnak. Azt is érdemes figyelembe venni, hogy milyen gyakran várható a különböző műveletek végrehajtása: elfogadható, ha olyan kialakítással rendelkezik, amely költséges műveletet tartalmaz, ha a művelet csak ritkán fordul elő.  

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
<li>Az Entity Group Transaction * (EGT) használatával megtarthatja az egységességet, ha egy részleg entitás módosítására van szükség, amikor az alkalmazotti entitást frissíti/behelyezi vagy törli. Ha például az egyes részlegek esetében fenntart egy részleg alkalmazottainak a darabszámát.</li>
</ul>
</td>
<td>
<ul>
<li>Előfordulhat, hogy egy alkalmazott és egy részleg entitást is le kell kérnie néhány ügyfél-tevékenységhez.</li>
<li>A tárolási műveletek ugyanabban a partícióban történnek. A nagy tranzakciós kötetek esetében ez egy hotspotot eredményezhet.</li>
<li>EGT használatával nem helyezhető át egy alkalmazott egy új részlegbe.</li>
</ul>
</td>
</tr>
<tr>
<td>Különálló entitások típusai, különböző partíciók, táblák vagy Storage-fiókok</td>
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
<li>EGT használatával nem helyezhető át egy alkalmazott egy új részlegbe.</li>
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

* További információ: entitás- [csoport tranzakciói](table-storage-design.md#entity-group-transactions)  


A lehetőségek közül választhat, és az előnyeit és hátrányait a legjelentősebb mértékben az adott alkalmazási helyzettől függ. Például azt, hogy milyen gyakran módosítja a részleg entitásait; az összes alkalmazotti lekérdezésnek szüksége van a további tanszéki információkra; Hogyan közelíti meg a partíciók skálázhatósági korlátait vagy a Storage-fiókját?  

## <a name="one-to-one-relationships"></a>Egy-az-egyhez kapcsolat
A tartományi modellek tartalmazhatnak egy-az-egyhez kapcsolatot az entitások között. Ha egy-az-egyhez kapcsolatot kell létrehoznia a Table serviceban, azt is ki kell választania, hogyan csatolja a két kapcsolódó entitást, ha mindkettőt le kell kérnie. Ez a hivatkozás implicit lehet, a kulcs értékeinek konvenciója alapján, vagy explicit módon, ha egy hivatkozást a **PartitionKey** és a **RowKey** értékek formájában tárol az egyes entitásokban a kapcsolódó entitáshoz. Ha meg szeretné tudni, hogy a kapcsolódó entitásokat ugyanabban a partícióban kell tárolnia, tekintse meg az [egy-a-többhöz kapcsolatok](#one-to-many-relationships)című szakaszt.  

Olyan implementációs megfontolások is megtalálhatók, amelyek egy-az-egyhez kapcsolatok megvalósítására vezethetnek a Table serviceban:  

* Nagyméretű entitások kezeléséhez (További információ: [nagyméretű entitások mintája](table-storage-design-patterns.md#large-entities-pattern)).  
* Hozzáférés-vezérlések implementálása (További információ: hozzáférés szabályozása közös hozzáférési aláírásokkal).  

## <a name="join-in-the-client"></a>Csatlakozás az ügyfélen
Bár a Table serviceban a kapcsolatok modellezése is lehetséges, ne feledje, hogy a Table service használatának két fő oka a méretezhetőség és a teljesítmény. Ha úgy találja, hogy számos olyan kapcsolatot modellez, amely veszélyezteti a megoldás teljesítményét és méretezhetőségét, meg kell kérdezni, hogy szükség van-e az összes adatkapcsolat létrehozására a tábla kialakításában. Lehet, hogy leegyszerűsíti a kialakítást, és javítja a megoldás méretezhetőségét és teljesítményét, ha lehetővé teszi, hogy az ügyfélalkalmazás bármilyen szükséges illesztést végezzen.  

Ha például olyan kis táblákat használ, amelyek gyakran nem változnak, akkor ezeket az adatfájlokat egyszer kell lekérnie, és az ügyfélen kell gyorsítótárazni. Ezzel elkerülhető, hogy az ismétlődő adatpontok ugyanazt az adatlekérdezést tudják lekérni. A jelen Útmutatóban bemutatott példákban a kisméretű szervezet részlegei valószínűleg kicsik lesznek, és ritkán változnak, hogy az ügyfélalkalmazás csak egyszer tölthető le és gyorsítótárazza az adatkeresési feladatait.  

## <a name="inheritance-relationships"></a>Öröklési kapcsolatok
Ha az ügyfélalkalmazás olyan osztályok készletét használja, amelyek egy öröklési kapcsolat részét képezik az üzleti entitások képviseletére, egyszerűen megtarthatja ezeket az entitásokat a Table service. Előfordulhat például, hogy a következő osztályok vannak meghatározva az ügyfélalkalmazásban, ahol a **személy** absztrakt osztály.

![Absztrakt személy osztálya](media/storage-table-design-guide/storage-table-design-IMAGE03.png)

A két konkrét osztály példányait megtarthatja a Table service egyetlen személy tábla használatával a következőhöz hasonló entitások használatával:  

![Személy tábla](media/storage-table-design-guide/storage-table-design-IMAGE04.png)

Ha többet szeretne megtudni arról, hogyan használható több entitás típusa ugyanabban a táblában az ügyfél kódjában, tekintse meg az útmutató későbbi, heterogén entitások típusának használata című szakaszát. Ez példákat tartalmaz arra, hogyan ismerhető fel az entitás típusa az ügyfél kódjában.  


## <a name="next-steps"></a>Következő lépések

- [Táblatervezési minták](table-storage-design-patterns.md)
- [Tervezés lekérdezéshez](table-storage-design-for-query.md)
- [Tábla adatai titkosítása](table-storage-design-encrypt-data.md)
- [Tervezés adatmódosításhoz](table-storage-design-for-modification.md)

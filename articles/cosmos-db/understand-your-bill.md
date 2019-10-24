---
title: A Azure Cosmos DB-számla ismertetése
description: Ez a cikk azt ismerteti, hogyan lehet megismerni a Azure Cosmos DB számlát néhány példával.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.reviewer: sngun
ms.openlocfilehash: 6d2edb7674a82a0388a0e028bee1b222e0e55004
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754730"
---
# <a name="understand-your-azure-cosmos-db-bill"></a>A Azure Cosmos DB-számla ismertetése

Teljes körűen felügyelt natív adatbázis-szolgáltatásként Azure Cosmos DB leegyszerűsíti a számlázást, mivel csak a kiosztott átviteli sebességért és a felhasznált tárterületért kell megszámolni. A helyszíni vagy a IaaS által üzemeltetett alternatívákhoz képest nincsenek további licencek, hardverek, használati költségek vagy létesítmények költségei. Ha figyelembe veszi a Azure Cosmos DB több régióra kiterjedő képességeit, az adatbázis-szolgáltatás jelentős mértékben csökkenti a költségeket a meglévő helyszíni vagy IaaS-megoldásokkal szemben.

A Azure Cosmos DB a kiosztott átviteli sebesség és a felhasznált tárterület alapján óradíjat számítunk fel. A kiépített átviteli sebesség esetében a számlázási egység 100 RU/s/óra, $0,008/óra díj ellenében, a szokásos nyilvános díjszabást feltételezve, a [díjszabást ismertető oldalon](https://azure.microsoft.com/pricing/details/cosmos-db/)talál. A felhasználható tárterület esetében havonta 1 GB-nyi tárterületet számolunk fel, a [díjszabási oldalon](https://azure.microsoft.com/pricing/details/cosmos-db/)pedig a $0,25. 

Ez a cikk néhány példát tartalmaz, amelyek segítenek megérteni a havi számlán látható részleteket. A példákban megjelenő számok eltérőek lehetnek, ha az Azure Cosmos-tárolók eltérő mennyiségű adatátviteli kapacitással rendelkeznek, ha több régióra kiterjednek, vagy egy adott hónapban eltérő időszakra futnak.

>! Megjegyzés: a számlázás a Wall órás óra bármely részén, nem 60 perces időtartam alatt történik.

## <a name="billing-examples"></a>Számlázási példák

### <a name="billing-example---throughput-on-a-container-full-month"></a>Számlázási példa – átviteli sebesség egy tárolón (teljes hónap)

* Tegyük fel, hogy egy tárolón 1 000 RU/s sebességű átviteli sebességet állít be, és a hónapra vonatkozóan 24 órán át * 30 nap = 720 óra összesen.  

* 1 000 RU/s a tárolók minden órájában 10 100 egység/óra/mp (azaz 1000/100 = 10). 

* A 10 egység/óra szorzata a $0,008-as (100 RU/s/óra/mp/óra) összegű óránkénti díj = $0,08. 

* A $0,08/óra szorzatával a hónapba tartozó órák számának értéke $0,08 * 24 óra * 30 nap = $57,60 a hónapra.  

* A havi számlán a 7 200 egység (100 RUs) fog megjelenni, amely a $57,60-as költséget fogja kiszámítani.

### <a name="billing-example---throughput-on-a-container-partial-month"></a>Számlázási példa – átviteli sebesség egy tárolón (részleges hónap)

* Tegyük fel, hogy létrehozunk egy tárolót a 2 500 RU/s kiosztott átviteli sebességgel. A tároló 24 órát vesz igénybe a hónapban (például a létrehozás után 24 órával töröljük azt).  

* Ezt követően 600 egység jelenik meg a számlán (2 500 RU/s/100 RU/mp/egység * 24 óra). A Cost $4,80 (600 egység * $0.008/egység) lesz.

* A havi számla összesen $4,80 lesz.

### <a name="billing-rate-if-storage-size-changes"></a>Számlázási sebesség, ha a tárolási méret megváltozik

A tárolási kapacitás elszámolási egysége a tárolt adatok havi időszakban (GB-ban) mért maximális óránkénti mennyisége. Ha például a hónap első felében 100 GB tárhelyet használt fel, a hónap második felében pedig 50 GB-ot, akkor az adott hónapban 75 GB adattárolásnak megfelelő használati díjat számítunk fel.

### <a name="billing-rate-when-container-or-a-set-of-containers-are-active-for-less-than-an-hour"></a>Számlázási sebesség, ha a tároló vagy a tárolók készlete egy óránál kevesebb ideig aktív

A tároló vagy az adatbázis óradíja minden órában megtörténik, függetlenül attól, hogy milyen a használat, vagy ha a tároló vagy az adatbázis egy óránál kevesebb ideig aktív. Ha például létrehoz egy tárolót vagy adatbázist, és 5 perccel később törli azt, akkor a számla egy órát fog tartalmazni.

### <a name="billing-rate-when-throughput-on-a-container-or-database-scales-updown"></a>A tároló vagy az adatbázis átviteli sebessége felfelé/lefelé méretezi a számlázási sebességet

Ha az 9:30-es számú kiépített átviteli sebességet a 400 RU/s értékről a 1 000 RU/s értékre emeli, és a kiosztott átviteli sebesség a 10:45-ben, a 400 ru/mp-ben lesz felszámítva. 

Ha egy tárolóra vagy tárolók egy készletére emeli a kiépített átviteli sebességet, a 9:30-es verzióban 100 – K RU/s – 200-K RU/mp, majd az alacsonyabb kiosztott átviteli sebesség (10:45) a 100-s RU/mp-ben történik, akkor két órányi, 200 K/s értékű díjat számítunk fel.

### <a name="billing-example-multiple-containers-each-with-dedicated-provisioned-throughput-mode"></a>Számlázási példa: több tároló, amelyek mindegyike dedikált kiosztott átviteli sebességgel rendelkezik

* Ha Azure Cosmos-fiókot hoz létre az USA 2. keleti régiójában, két tárolóban, a 500 RU/s és a 700 RU/mp kiépített átviteli sebességgel, akkor a 1 200 RU/s teljes kiosztott átviteli sebességgel rendelkezhet.  

* A díj 1200/100 * $0,008 = $0.096/óra. 

* Ha az átviteli sebességre vonatkozó igények módosulnak, és az egyes tárolók kapacitását 500 RU/s értékre emelte, miközben egy új, korlátlan számú, 20 000 RU/s méretű tárolót hozott létre, a teljes kiosztott kapacitás 22 200 RU/s (1 000 RU/SEC + 1 200 RU/SEC + 20, ru/s).  

* A számla ezután a következőre változik: $0,008 x 222 = $1.776/Hour. 

* 720 óra (24 óra * 30 nap), ha 500 óra kiépített átviteli sebesség volt a 1 200 RU/s, a fennmaradó 220 óra kiépített átviteli sebesség pedig 22 200 RU/s, a havi számla a következőket mutatja be: 500 x $0.096/Hour + 220 x $1.776/óra = $438.72/month.

![Dedikált átviteli sebesség – példa](./media/understand-your-bill/bill-example1.png)

### <a name="billing-example-containers-with-shared-throughput-mode"></a>Számlázási példa: megosztott átviteli sebességű tárolók

* Ha Azure Cosmos-fiókot hoz létre az USA 2. keleti régiójában két Azure Cosmos-adatbázissal (az átviteli sebességet az adatbázis szintjén megosztó tárolók készletével), akkor a 50-K RU/s és 70-K/s értékkel rendelkező kiépített átviteli sebességnél teljes kiépített 120 K/mp átviteli sebesség.  

* 1200 x $0,008 = $9.60/óra díjat számítunk fel. 

* Ha az átviteli sebességre vonatkozó igények módosulnak, és minden adatbázishoz 10K RU/s értékkel növelte az egyes adatbázisok kiosztott átviteli sebességét, és egy új tárolót ad hozzá az első adatbázishoz, amelynek dedikált átviteli sebessége 15 – K RU/mp a megosztott átviteli sebességű adatbázisra, a teljes kiosztott kapacitás 155-K RU/s (60 K/s/s + 80 s RU/s + 15 K RU/s).  

* A számla ezután a következőre változik: 1 550 * $0,008 = $12.40/óra.  

* 720 óra alatt, ha 300 óra kiépített átviteli sebesség volt a 120-K RU/s, és a fennmaradó 420 óra kiépített átviteli sebesség a következő volt: 155-K RU/s, a havi számla az alábbiak szerint fog megjelenni: 300 x $9.60/Hour + 420 x $12.40/óra = $2 880 + $5 208 = $8088/month. 

![Megosztott átviteli sebesség – példa](./media/understand-your-bill/bill-example2.png)

## <a name="billing-examples-with-geo-replication-and-multi-master"></a>Számlázási példák a Geo-replikációval és a több főkiszolgálóval  

Bármikor hozzáadhat vagy eltávolíthat Azure-régiókat a világ bármely pontján az Azure Cosmos-adatbázis fiókjához. A különböző Azure Cosmos-adatbázisok és-tárolók számára konfigurált átviteli sebesség az Azure Cosmos Database-fiókhoz társított összes Azure-régióban le lesz foglalva. Ha a kiépített átviteli sebesség (RU/s) összege az Azure Cosmos Database-fiókban (óránként kiépítve) található összes adatbázisban és tárolóban konfigurálva van, az adatbázis-fiókjához társított Azure-régiók száma N, akkor a teljes egy adott órára kiépített átviteli sebesség az Azure Cosmos Database-fiókhoz (a), amely egyetlen írási régióval van konfigurálva, egyenlő a T x N RU/s értékkel, és (b) minden olyan régióban konfigurálva van, amely képes a feldolgozásra. illetve. Kiépített átviteli sebesség (egyszeri írási régió): $0.008/óra/100 RU/s és kiépített átviteli sebesség több írható régióval (több főkiszolgálós konfigurációval) $0.016/óra/óránként 100 RU/s (lásd a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/cosmos-db/)). Akár egyetlen írási régiója, akár több írási régiója is van, Azure Cosmos DB lehetővé teszi az adatok bármely régióból való beolvasását.

### <a name="billing-example-multi-region-azure-cosmos-account-single-region-writes"></a>Számlázási példa: több régióból álló Azure Cosmos-fiók, egyrégiós írások

Tegyük fel, hogy rendelkezik egy Azure Cosmos-tárolóval az USA nyugati régiójában. A tároló a következővel jön létre: 10 000 RU/s, és ebben a hónapban 1 TB adat tárolására kerül. Tegyük fel, hogy három régiót (az USA keleti régiója, Észak-Európa és Kelet-Ázsia) vesz fel az Azure Cosmos-fiókjába, amelyek mindegyike azonos tárterülettel és átviteli sebességgel rendelkezik. A teljes havi számla a következő lesz (feltéve, hogy havonta 30 nap van megadva). A számla a következőképpen alakul: 

|**Elem** |**Használat (hónap)** |**Sebessége** |**Havi költség** |
|---------|---------|---------|-------|
|Átviteli sebességre vonatkozó számla az USA nyugati régiójában lévő tároló esetén      | 10K RU/s * 24 * 30    |$0,008/100 RU/s/óra   |$576|
|Átviteli sebességre vonatkozó számla 3 további régió esetén – az USA keleti régiója, Észak-Európa, Kelet-Ázsia       | 3 * 10K RU/mp * 24 * 30    |$0,008/100 RU/s/óra  |$1 728|
|Adattárolásra vonatkozó számla az USA nyugati régiójában lévő tároló esetén      | 250 GB    |0,25/GB  |$62,50|
|Adattárolásra vonatozó számla 3 további régió esetén – az USA keleti régiója, Észak-Európa, Kelet-Ázsia      | 3 * 250 GB    |0,25/GB  |$187,50|
|**Teljes**     |     |  |**$2 554**|

*Tegyük fel, hogy az USA nyugati régiójában lévő tárolóból havonta 100 GB-nyi adatról van az adatok replikálása az USA keleti régiójában, Észak-Európában és Kelet-Ázsia. A kimenő forgalomért az adatátviteli díjszabás szerint számítunk fel díjat.*

### <a name="billing-example-multi-region-azure-cosmos-account-multi-region-writes"></a>Számlázási példa: több régióból álló Azure Cosmos-fiók, többrégiós írások

Tegyük fel, hogy létrehoz egy Azure Cosmos-tárolót az USA nyugati régiójában. A tároló a következővel jön létre: 10 000 RU/s, és ebben a hónapban 1 TB adat tárolására kerül. Tegyük fel, hogy három régiót vesz fel (az USA keleti régiója, Észak-Európa és Kelet-Ázsia), amelyek mindegyike azonos tárterülettel és átviteli sebességgel rendelkezik, és szeretné írni a tárolókat az Azure Cosmos-fiókhoz társított összes régióban. A havi számla összegét a következő módon számítjuk fel:

|**Elem** |**Használat (hónap)**|**Sebessége** |**Havi költség** |
|---------|---------|---------|-------|
|Adatátviteli számla az USA nyugati régiójában lévő tárolóhoz (az összes régió írható)       | 10K RU/s * 24 * 30    |$0,016/100 RU/s/óra    |$1 152 |
|Adatátviteli számla 3 további régióhoz – az USA keleti régiója, Észak-Európa és Kelet-Ázsia (minden régió írható)        | (3 + 1) * 10K RU/mp * 24 * 30    |$0,016/100 RU/s/óra   |$4 608 |
|Adattárolásra vonatkozó számla az USA nyugati régiójában lévő tároló esetén      | 250 GB    |0,25/GB  |$62,50|
|Adattárolásra vonatozó számla 3 további régió esetén – az USA keleti régiója, Észak-Európa, Kelet-Ázsia      | 3 * 250 GB    |0,25/GB  |$187,50|
|**Teljes**     |     |  |**$6 010**|

*Tegyük fel, hogy az USA nyugati régiójában lévő tárolóból havonta 100 GB-nyi adatról van az adatok replikálása az USA keleti régiójában, Észak-Európában és Kelet-Ázsia. A kimenő forgalomért az adatátviteli díjszabás szerint számítunk fel díjat.*

### <a name="billing-example-azure-cosmos-account-with-multi-master-database-level-throughput-including-dedicated-throughput-mode-for-some-containers"></a>Számlázási példa: Azure Cosmos-fiók több főkiszolgálós, adatbázis-szintű átviteli sebességgel, beleértve a dedikált átviteli módot egyes tárolók esetében

Tekintsük át a következő példát, ahol egy többrégiós Azure Cosmos-fiók van, ahol minden régió írható (több főkiszolgálós konfiguráció). Az egyszerűség kedvéért feltételezzük, hogy a tárterület mérete állandó marad, és nem változik, és kihagyja itt, hogy a példa egyszerűbb legyen. A hónap során kiosztott átviteli sebesség a következőképpen változhat (feltéve, hogy 30 nap vagy 720 óra): 

[0-100 óra]:  

* Létrehoztunk egy három régiós Azure Cosmos-fiókot (USA nyugati régiója, USA keleti régiója, Észak-Európa), ahol minden régió írható 

* Létrehoztunk egy adatbázist (D1) megosztott átviteli sebességgel (10K RU/s) 

* Létrehoztunk egy adatbázist (D2), amelynek közös átviteli sebessége 30 – K RU/s  

* Létrehoztunk egy tárolót (C1) a dedikált átviteli sebességgel 20 K/s sebességgel 

[101-200 óra]:  

* Az adatbázis (D1) felskálázása 50 K/s-re 

* Az adatbázis (D2) felskálázása 70 K/s-re  

* A tároló (C1) törölve  

[201-300 óra]:  

* A tárolót (C1) ismét létrehozta a dedikált átviteli sebesség 20 K/s sebességgel 

[301-400 óra]:  

* Eltávolítjuk az egyik régiót az Azure Cosmos-fiókból (az írható régiók száma most 2) 

* Az adatbázis (D1) a következőre lett méretezve – 10K RU/s 

* Az adatbázis (D2) felskálázása 80 K/s-re  

* A tároló (C1) ismét törölve 

[401-500 óra]:  

* Az adatbázis (D2) méretét a következőre csökkentették – 10K RU/s  

* A tárolót (C1) ismét létrehozta a dedikált átviteli sebesség 20 K/s sebességgel 

[501-700 óra]:  

* Adatbázis (D1) felskálázása 20 K/s-re  

* Az adatbázis (D2) felskálázása 100 K/s-re  

* A tároló (C1) ismét törölve  

[701-720 óra]:  

* Az adatbázis (D2) felskálázása 50 K/s-re  

Az alábbi ábrán látható, hogy a teljes kiépített átviteli sebesség változásai a hónapban 720 óra alatt jelennek meg: 

![Valós élet – példa](./media/understand-your-bill/bill-example3.png)

A teljes havi számla (feltéve, hogy a havi 30 nap/720 óra) a következőképpen lesz kiszámítva:

|**Óra**  |**RU/s** |**Elem** |**Használat (óránként)** |**Költségek** |
|---------|---------|---------|-------|-------|
|[0-100] |D1:10K <br/>D2:30K <br/>C1:20000 |Adatátviteli számla az USA nyugati régiójában lévő tárolóhoz (az összes régió írható)  | `D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 30 K RU/sec/100 * $0.016 * 100 hours = $480` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$960  |
| | |Átviteli sebesség 2 további régióban: USA keleti régiója, Észak-Európa (az összes régió írható)  |`(2 + 1) * (60 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |$2 880  |
|[101-200] |D1:50K <br/>D2:70K <br/>C1:-- |Adatátviteli számla az USA nyugati régiójában lévő tárolóhoz (az összes régió írható)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` |$1920  |
| | |Átviteli sebesség 2 további régióban: USA keleti régiója, Észak-Európa (az összes régió írható)  |`(2 + 1) * (120 K RU/sec /100 * $0.016) * 100 hours = $5,760`  |$5 760  |
|[201-300]  |D1:50K <br/>D2:70K <br/>C1:20000 |Adatátviteli számla az USA nyugati régiójában lévő tárolóhoz (az összes régió írható)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$2 240  |
| | |Átviteli sebesség 2 további régióban: USA keleti régiója, Észak-Európa (az összes régió írható)  |`(2 + 1) * (140 K RU/sec /100 * $0.016-) * 100 hours = $6,720` |$6 720 |
|[301-400] |D1:10K <br/>D2:80K <br/>C1:-- |Adatátviteli számla az USA nyugati régiójában lévő tárolóhoz (az összes régió írható)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 80 K RU/sec/100 * $0.016 * 100 hours = $1,280`  |$1 440   |
| | |Átviteli sebesség 2 további régióban: USA keleti régiója, Észak-Európa (az összes régió írható)  |`(1 + 1) * (90 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |$2 880  |
|[401-500] |D1:10K <br>D2:10K <br>C1:20000 |Adatátviteli számla az USA nyugati régiójában lévő tárolóhoz (az összes régió írható)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`D2: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$640  |
| | |Átviteli sebesség 2 további régióban: USA keleti régiója, Észak-Európa (az összes régió írható)  |`(1 + 1) * (40 K RU/sec /100 * $0.016) * 100 hours = $1,280`  |$1 280  |
|[501-700] |D1:20000 <br>D2:100K <br>C1:-- |Adatátviteli számla az USA nyugati régiójában lévő tárolóhoz (az összes régió írható)  |`D1: 20 K RU/sec/100 * $0.016 * 200 hours = $640` <br>`D2: 100 K RU/sec/100 * $0.016 * 200 hours = $3,200` |$3 840  |
| | |Átviteli sebesség 2 további régióban: USA keleti régiója, Észak-Európa (az összes régió írható)  |`(1 + 1) * (120 K RU/sec /100 * $0.016) * 200 hours = $1,280`  |$7 680  |
|[701-720] |D1:20000 <br/>D2:50K <br/>C1:-- |Adatátviteli számla az USA nyugati régiójában lévő tárolóhoz (az összes régió írható)  |`D1: 20 K RU/sec/100 *$0.016 * 20 hours = $64` <br/>`D2: 50 K RU/sec/100 *$0.016 * 20 hours = $160` |$224  |
| | |Átviteli sebesség 2 további régióban: USA keleti régiója, Észak-Európa (az összes régió írható)  |`(1 + 1) * (70 K RU/sec /100 * $0.016) * 20 hours = $448`  |$224  |
|| |**Teljes havi költség**  | |**$38 688**   |

## <a name="proactively-estimating-your-monthly-bill"></a>A havi számla proaktív becslése  

Vegyünk egy másik példát, amelyben a hónap végéig proaktívan kell megbecsülni a számlát. A számlát a következőképpen becsülheti meg:

|**Tárolási díj** | |
|----|----|
|Átlagos rekord mérete (KB) |1 |
|Rekordok száma  |100 000 000  |
|Teljes tárterület (GB)  |100 |
|Havi költség GB-onként  |$0,25  |
|A tárolás várható havi díja   |$25,00  |

<br>

|**Átviteli sebesség** | | | |
|----|----|----|----|
|Művelettípus| Kérelmek/másodperc| AVG. RU/kérelem| RUs szükséges|
|Írás| 100 | 5 | 500|
|Olvasás| 400| 1| 400|

Összes RU/MP: 500 + 400 = 900 óránkénti költség: 900/100 * $0,008 = $0,072 az átviteli sebesség várható havi költsége (31 nap): $0,072 * 24 * 31 = $53,57

**Teljes havi költség**

Teljes havi költség = havi költség a tároláshoz és az átviteli sebesség teljes havi költsége = $25,00 + $53,57 = $78,57

*A díjszabás régiónként eltérő lehet. Naprakész díjszabásért tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/cosmos-db/).*

## <a name="billing-with-azure-cosmos-db-reserved-capacity"></a>Számlázás Azure Cosmos DB fenntartott kapacitással

Azure Cosmos DB fenntartott kapacitás lehetővé teszi a kiépített átviteli sebesség előzetes (fenntartott kapacitás vagy foglalás) megvásárlását, amely alkalmazható az összes Azure Cosmos-adatbázisra és-tárolóra (bármely API-vagy adatmodell esetében) az összes Azure-régióban. Mivel a kiépített átviteli sebesség régiónként változik, segít a fenntartott kapacitásnak a kedvezményben megvásárolt pénzügyi kreditként való megszerzésében, amely az egyes régiókban az adott díjszabás alapján kiépített átviteli sebességig vehető igénybe. Tegyük fel például, hogy rendelkezik egy Azure Cosmos-fiókkal, amely egyetlen, 50-es RU/s-vel kiépített tárolóval rendelkezik, és globálisan replikálta két régiót – az USA keleti régiója és Kelet-Japán. Ha az utólagos elszámolású lehetőséget választja, akkor a következő díjat kell fizetnie:  

* az USA keleti régiójában: a 50-s RU/s értéknél, a régióban $0,008/100 

* a Kelet-japán régióban: 50-s RU/s a régió $0,009/100

A teljes számla (fenntartott kapacitás nélkül) (feltéve, hogy 30 nap vagy 720 óra): 

|**Régió**| **Óránkénti ár/100 RU/s**|**Egység (RU/s)**|**Számlázott összeg (óránként)**| **Számlázott összeg (havonta)**|
|----|----|----|----|----|
|USA keleti régiója|$0,008 |50 K|$4|$2 880 |
|Kelet-Japán|$0,009 |50 K| $4,50 |$3 240 |
|Összes|||$8,50|$6 120 |

Vegyük figyelembe, hogy a lefoglalt kapacitást is megvásárolta. Fenntartott kapacitást vásárolhat a 100 – K RU/s számára a $56 064-es díj egy évig (20%-os kedvezménnyel), vagy $6,40/óra áron. A fenntartott kapacitás díjszabása a [díjszabási oldalon](https://azure.microsoft.com/pricing/details/cosmos-db/)található.  

* Átviteli sebesség (utólagos elszámolású): 100 000 RU/s/100 * $0.008/óra * 8760 óra egy évben = $70 080 

* A maximális átviteli sebesség (fenntartott kapacitással) $70 080 20% = $56 064 

A ténylegesen megvásárolt kreditek összege $8 óránként, 100 K/s áron, az USA keleti régiójában, a $6,40/óra díjszabása alapján. Ezt követően az előre fizetett átviteli sebesség foglalása óránként elvégezhető a kiépített átviteli kapacitáshoz bármely globális Azure-régióban az előfizetésre vonatkozó helyi lista díjszabása alapján. Ebben a példában, ahol az USA keleti régiójában és Kelet-Japánban 50 K/s üzembe helyezése történik, a $8,00-os kiépített átviteli sebesség óránként, a díj pedig a $0,50/óra (vagy $360/hó) alapján történik. 

|**Régió**| **Óránkénti ár/100 RU/s**|**Egység (RU/s)**| **Számlázott összeg (óránként)**| **Számlázott összeg (havonta)**|
|----|----|----|----|----|
|USA keleti régiója|$0,008 |50 K|$4|$2 880 |
|Kelet-Japán|$0,009 |50 K| $4,50 |$3 240 |
|||Utólagos, használatalapú fizetés|$8,50|$6120|
|Vásárolt fenntartott kapacitás|$0,0064 (20% kedvezmény) |100 RU/s vagy $8 kapacitás előre megvásárolva |– $8|– $5 760 |
|Nettó számla|||$0,50 |$360 |

## <a name="next-steps"></a>Következő lépések

A következő cikkekkel folytathatja a Azure Cosmos DB a Cost Optimization megismerését:

* További információ arról [, hogy a Cosmos db díjszabási modellje mennyire költséghatékony az ügyfelek számára](total-cost-ownership.md)
* További információ a [fejlesztés és a tesztelés optimalizálásáról](optimize-dev-test.md)
* További információ az [átviteli sebesség optimalizálásáról](optimize-cost-throughput.md)
* További információ a [tárolási díjak optimalizálásáról](optimize-cost-storage.md)
* További információ [az olvasási és írási díjak optimalizálásáról](optimize-cost-reads-writes.md)
* További információ [a lekérdezések díjszabásának optimalizálásáról](optimize-cost-queries.md)
* További információ [a több régióból álló Azure Cosmos-fiókok díjainak optimalizálásáról](optimize-cost-regions.md)

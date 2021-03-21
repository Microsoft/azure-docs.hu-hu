---
title: A Azure Cosmos DB-számla ismertetése
description: Ez a cikk azt ismerteti, hogyan lehet megismerni a Azure Cosmos DB számlát néhány példával.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2020
ms.reviewer: sngun
ms.openlocfilehash: b0d27cb2f941db374a144cbd5c026444730e24e8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98247488"
---
# <a name="understand-your-azure-cosmos-db-bill"></a>Az Azure Cosmos DB számláinak ismertetése
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Teljes körűen felügyelt natív adatbázis-szolgáltatásként Azure Cosmos DB leegyszerűsíti a számlázást, mivel csak az adatbázis-műveletekhez és a felhasznált tárolóhoz töltődik. A helyszíni vagy a IaaS által üzemeltetett alternatívákhoz képest nincsenek további licencek, hardverek, használati költségek vagy létesítmények költségei. Ha figyelembe veszi a Azure Cosmos DB több régióra kiterjedő képességeit, az adatbázis-szolgáltatás jelentős mértékben csökkenti a költségeket a meglévő helyszíni vagy IaaS-megoldásokkal szemben.

- **Adatbázis-műveletek**: az adatbázis-műveletekért felszámított módszer a használt Azure Cosmos-fiók típusától függ.

  - **Kiépített átviteli sebesség**: óradíjat számolunk fel a maximális kiosztott átviteli sebességért egy adott órában, 100 ru/s-onként.
  - **Kiszolgáló** nélküli: az adatbázis-műveletek által felhasznált kérelmek teljes mennyiségét óránként számlázjuk.

- **Tárolás**: az adat-és indexek által az adott órában felhasznált teljes tárterület (GB-ban) átalány-díjait számítjuk fel.

A legfrissebb díjszabási információkért tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/cosmos-db/) .

Ez a cikk néhány példán keresztül elmagyarázza, mit jelentenek a havi számla különböző részei. A példákban szereplő számok az Önétől eltérőek lehetnek, ha az Ön Azure Cosmos-tárolói számára kiosztott átviteli sebesség eltérő, ha több régióra kiterjednek, vagy eltérő hosszúságú időszakban futnak havonta. A jelen cikkben szereplő összes példa a [díjszabási oldalon](https://azure.microsoft.com/pricing/details/cosmos-db/)látható díjszabási információk alapján számítja ki a számlát.

> [!NOTE]
> A számlázás a fali óra bármely részén, nem 60 perces időtartamban történik. Az ebben a dokumentációban látható összes példa az USA-ban nem kormányzati régióban üzembe helyezett Azure Cosmos-fiók díjszabásán alapul. A díjszabás és a számítás a használt régiótól függően változhat, a legfrissebb díjszabási információkért tekintse meg a [Azure Cosmos db díjszabási oldalát](https://azure.microsoft.com/pricing/details/cosmos-db/) .

## <a name="billing-examples"></a>Számlázási példák

### <a name="billing-example---provisioned-throughput-on-a-container-full-month"></a>Számlázási példa – kiosztott átviteli sebesség egy tárolón (teljes hónap)

* Tegyük fel, hogy egy tárolón 1 000 RU/s sebességű átviteli sebességet állít be, és a hónapra vonatkozóan 24 órán át * 30 nap = 720 óra összesen.  

* 1 000 RU/s a tárolók minden órájában 10 100 egység/óra/mp (azaz 1000/100 = 10). 

* A 10 egység/óra szorzata a $0,008-as (100 RU/s/óra/mp/óra) összegű óránkénti díj = $0,08. 

* A $0,08/óra szorzatával a hónapba tartozó órák számának értéke $0,08 * 24 óra * 30 nap = $57,60 a hónapra.  

* A havi számlán a 7 200 egység (100 RUs) fog megjelenni, amely a $57,60-as költséget fogja kiszámítani.

### <a name="billing-example---provisioned-throughput-on-a-container-partial-month"></a>Számlázási példa – kiosztott átviteli sebesség egy tárolón (részleges hónap)

* Tegyük fel, hogy létrehozunk egy tárolót a 2 500 RU/s kiosztott átviteli sebességgel. A tároló 24 órát vesz igénybe a hónapban (például a létrehozás után 24 órával töröljük azt).  

* Ezt követően 600 egység jelenik meg a számlán (2 500 RU/s/100 RU/mp/egység * 24 óra). A Cost $4,80 (600 egység * $0.008/egység) lesz.

* A havi számla összesen $4,80 lesz.

### <a name="billing-example---serverless-container"></a>Számlázási példa – kiszolgáló nélküli tároló

* Tegyük fel, hogy létrehozunk egy kiszolgáló nélküli tárolót. 

* Egy hónap alatt kiállítjuk az adatbázis-kérelmeket, amelyek összesen 500 000 kérelem-egységet vesznek igénybe. A díj $0,125 lesz (500 000 * $0,25/millió).

* A havi számla összesen $0,125 lesz.

### <a name="billing-rate-if-storage-size-changes"></a>Számlázási sebesség, ha a tárolási méret megváltozik

A tárolási kapacitást a rendszer a havi időszakon belül, GB-ban tárolt maximális óránkénti mennyiségű egységben számlázza. Ha például 100 GB tárhelyet használ a hónap felében, és 50 GB-ot a hónap második felében, akkor az adott hónapban 75 GB tárterülettel egyenértékű díjat számítunk fel.

### <a name="billing-rate-when-container-or-a-set-of-containers-are-active-for-less-than-an-hour"></a>Számlázási sebesség, ha a tároló vagy a tárolók készlete egy óránál kevesebb ideig aktív

A tároló vagy az adatbázis óradíja minden órában megtörténik, függetlenül attól, hogy milyen a használat, vagy ha a tároló vagy az adatbázis egy óránál kevesebb ideig aktív. Ha például létrehoz egy tárolót vagy adatbázist, és 5 perccel később törli azt, akkor a számla egy órát fog tartalmazni.

### <a name="billing-rate-when-provisioned-throughput-on-a-container-or-database-scales-updown"></a>Adatátviteli sebesség egy tárolón vagy adatbázison felfelé/lefelé

Ha az 9:30-es számú kiépített átviteli sebességet a 400 RU/s értékről a 1 000 RU/s értékre emeli, és a kiosztott átviteli sebesség a 10:45-ben, a 400 ru/mp-ben lesz felszámítva. 

Ha egy tárolóra vagy tárolók egy készletére emeli a kiépített átviteli sebességet, a 9:30-es verzióban 100 – K RU/s – 200-K RU/mp, majd az alacsonyabb kiosztott átviteli sebesség (10:45) a 100-s RU/mp-ben történik, akkor két órányi, 200 K/s értékű díjat számítunk fel.

### <a name="billing-example-multiple-containers-each-with-dedicated-provisioned-throughput-mode"></a>Számlázási példa: több tároló, amelyek mindegyike dedikált kiosztott átviteli sebességgel rendelkezik

* Ha Azure Cosmos-fiókot hoz létre az USA 2. keleti régiójában, két tárolóban, a 500 RU/s és a 700 RU/mp kiépített átviteli sebességgel, akkor a 1 200 RU/s teljes kiosztott átviteli sebességgel rendelkezhet.  

* A díj 1200/100 * $0,008 = $0.096/óra. 

* Ha az átviteli sebességre vonatkozó igények módosulnak, és az egyes tárolók kapacitását 500 RU/s értékre emelte, miközben egy új, korlátlan számú, 20 000 RU/s méretű tárolót hozott létre, a teljes kiosztott kapacitás 22 200 RU/s (1 000 RU/SEC + 1 200 RU/SEC + 20, ru/s).  

* A számla ezután a következőre változik: $0,008 x 222 = $1.776/Hour. 

* 720 óra (24 óra * 30 nap), ha 500 óra kiépített átviteli sebesség volt a 1 200 RU/s, a fennmaradó 220 óra kiépített átviteli sebesség pedig 22 200 RU/s, a havi számla a következőket mutatja be: 500 x $0.096/Hour + 220 x $1.776/óra = $438.72/month.

:::image type="content" source="./media/understand-your-bill/bill-example1.png" alt-text="Dedikált átviteli sebesség – példa":::

### <a name="billing-example-containers-with-shared-provisioned-throughput-mode"></a>Számlázási példa: tárolók megosztott (kiépített) átviteli móddal

* Ha Azure Cosmos-fiókot hoz létre az USA 2. keleti régiójában két Azure Cosmos-adatbázissal (az adatátviteli sebességet az adatbázis szintjén megosztó tárolók készletével), a 50 – K RU/s és 70-K/s értékkel rendelkező kiépített átviteli sebesség esetében pedig a 120 K/s teljes kiosztott átviteli sebességgel rendelkezhet.  

* 1200 x $0,008 = $9.60/óra díjat számítunk fel. 

* Ha az átviteli sebességre vonatkozó igények módosulnak, és az egyes adatbázisok kiosztott átviteli sebességét minden adatbázis esetében a 10K RU/mp értékkel növelte. Emellett új tárolót ad hozzá az első adatbázishoz, amelynek dedikált átviteli sebessége 15 – K RU/s a megosztott átviteli sebességű adatbázishoz, a teljes kiosztott kapacitás 155-K RU/s (60 K/s/s + 80 K/s/s + 15 K/s).  

* A számla ezután a következőre változik: 1 550 * $0,008 = $12.40/óra.  

* 720 óra alatt, ha 300 óra kiépített átviteli sebesség volt a 120-K RU/s, és a fennmaradó 420 óra kiépített átviteli sebesség a következő volt: 155-K RU/s, a havi számla az alábbiak szerint fog megjelenni: 300 x $9.60/Hour + 420 x $12.40/óra = $2 880 + $5 208 = $8088/month. 

:::image type="content" source="./media/understand-your-bill/bill-example2.png" alt-text="Megosztott átviteli sebesség – példa":::

## <a name="billing-examples-with-geo-replication"></a>Számlázási példák földrajzi replikálással  

Bármikor hozzáadhat vagy eltávolíthat Azure-régiókat a világ bármely pontján az Azure Cosmos-adatbázis fiókjához. A különböző Azure Cosmos-adatbázisok és-tárolók számára konfigurált átviteli sebesség az Azure Cosmos Database-fiókhoz társított összes Azure-régióban le lesz foglalva. Ha a kiépített átviteli sebesség (RU/mp) az Azure Cosmos Database-fiókban (óránként kiépítve) található összes adatbázisban és tárolóban konfigurálva van, az adatbázis-fiókhoz társított Azure-régiók száma N, akkor az Azure Cosmos Database-fiókhoz tartozó teljes kiosztott átviteli sebesség a T x N RU/s értékkel egyenlő. Kiépített átviteli sebesség (egyszeres írási régió): $0.008/óra/100 RU/s és kiépített átviteli sebesség több írható régióval (többrégiós írási konfiguráció) költségek $0.016/óra/óránként 100 RU/s (lásd a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/cosmos-db/)). Akár egyetlen írási régiója, akár több írási régiója is van, Azure Cosmos DB lehetővé teszi az adatok bármely régióból való beolvasását.

### <a name="billing-example-multi-region-azure-cosmos-account-single-region-writes"></a>Számlázási példa: több régióból álló Azure Cosmos-fiók, egyrégiós írások

Tegyük fel, hogy rendelkezik egy Azure Cosmos-tárolóval az USA nyugati régiójában. A tároló a következővel jön létre: 10 000 RU/s, és ebben a hónapban 1 TB adat tárolására kerül. Tegyük fel, hogy három régiót (az USA keleti régiója, Észak-Európa és Kelet-Ázsia) vesz fel az Azure Cosmos-fiókjába, amelyek mindegyike azonos tárterülettel és átviteli sebességgel rendelkezik. A teljes havi számla a következő lesz (feltéve, hogy havonta 30 nap van megadva). A számla a következőképpen alakul: 

|**Elem** |**Használat (hónap)** |**Sebesség** |**Havi költség** |
|---------|---------|---------|-------|
|Adatátviteli számla az USA nyugati régiójában lévő tárolóhoz      | 10K RU/s * 24 * 30    |$0,008/100 RU/s/óra   |$576|
|Adatátviteli számla 3 további régióhoz – az USA keleti régiója, Észak-Európa és Kelet-Ázsia       | 3 * 10K RU/mp * 24 * 30    |$0,008/100 RU/s/óra  |$1 728|
|Storage-számla az USA nyugati régiójában lévő tárolóhoz      | 250 GB    |0,25/GB  |$62,50|
|Storage-számla 3 további régióhoz – az USA keleti régiója, Észak-Európa és Kelet-Ázsia      | 3 * 250 GB    |0,25/GB  |$187,50|
|**Összesen**     |     |  |**$2 554**|

*Tegyük fel, hogy az USA nyugati régiójában lévő tárolóból havonta 100 GB-nyi adatról van az adatok replikálása az USA keleti régiójában, Észak-Európában és Kelet-Ázsia. A kimenő forgalomért az adatátviteli díjszabás szerint számítunk fel díjat.*

### <a name="billing-example-multi-region-azure-cosmos-account-multi-region-writes"></a>Számlázási példa: több régióból álló Azure Cosmos-fiók, többrégiós írások

Tegyük fel, hogy létrehoz egy Azure Cosmos-tárolót az USA nyugati régiójában. A tároló a következővel jön létre: 10 000 RU/s, és ebben a hónapban 1 TB adat tárolására kerül. Tegyük fel, hogy három régiót vesz fel (az USA keleti régiója, Észak-Európa és Kelet-Ázsia), amelyek mindegyike azonos tárterülettel és átviteli sebességgel rendelkezik, és szeretné írni a tárolókat az Azure Cosmos-fiókhoz társított összes régióban. A havi számla összegét a következő módon számítjuk fel:

|**Elem** |**Használat (hónap)**|**Sebesség** |**Havi költség** |
|---------|---------|---------|-------|
|Adatátviteli számla az USA nyugati régiójában lévő tárolóhoz (az összes régió írható)       | 10K RU/s * 24 * 30    |$0,016/100 RU/s/óra    |$1 152 |
|Adatátviteli számla 3 további régióhoz – az USA keleti régiója, Észak-Európa és Kelet-Ázsia (minden régió írható)        | 3 * 10K RU/mp * 24 * 30    |$0,016/100 RU/s/óra   |$3 456 |
|Storage-számla az USA nyugati régiójában lévő tárolóhoz      | 250 GB    |0,25/GB  |$62,50|
|Storage-számla 3 további régióhoz – az USA keleti régiója, Észak-Európa és Kelet-Ázsia      | 3 * 250 GB    |0,25/GB  |$187,50|
|**Összesen**     |     |  |**$6 010**|

*Tegyük fel, hogy az USA nyugati régiójában lévő tárolóból havonta 100 GB-nyi adatról van az adatok replikálása az USA keleti régiójában, Észak-Európában és Kelet-Ázsia. A kimenő forgalomért az adatátviteli díjszabás szerint számítunk fel díjat.*

### <a name="billing-example-azure-cosmos-account-with-multi-region-writes-database-level-throughput-including-dedicated-throughput-mode-for-some-containers"></a>Számlázási példa: Azure Cosmos-fiók többrégiós írásokkal, adatbázis-szintű teljesítmény, beleértve a dedikált átviteli módot egyes tárolók esetében

Tekintsük át a következő példát, ahol olyan többrégiós Azure Cosmos-fiókkal rendelkezünk, amelyben minden régió írható (több írási régiót tartalmazó konfiguráció). Az egyszerűség kedvéért feltételezzük, hogy a tárterület mérete állandó marad, és nem változik, és kihagyja itt, hogy a példa egyszerűbb legyen. A hónap során kiosztott átviteli sebesség a következőképpen változhat (feltéve, hogy 30 nap vagy 720 óra): 

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

:::image type="content" source="./media/understand-your-bill/bill-example3.png" alt-text="Valós élet – példa":::

A teljes havi számla (feltéve, hogy a havi 30 nap/720 óra) a következőképpen lesz kiszámítva:

|**Órák**  |**RU/s** |**Elem** |**Használat (óránként)** |**Költségek** |
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

## <a name="billing-examples-with-azure-cosmos-db-free-tier-accounts"></a><a id="azure-free-tier"></a>Számlázási példák Azure Cosmos DB ingyenes szintű fiókokkal

A Azure Cosmos DB ingyenes csomaggal az első 400 RU/s és 5 GB tárterületet ingyenesen veheti fel a fiókjába, a fiók szintjén alkalmazva. Az összes RU/s, valamint a 400 RU/s és 5 GB-nál nagyobb tárterület számlázása a díjszabási oldalon érvényes díjszabási díjszabás szerint történik. A számlán nem fog megjelenni az ingyenes 400 ru/s és 5 GB-os díj vagy vonal tétel, csak az RU/s és a tárhely, amely az ingyenes szinten lefedett. Az 400 RU/s bármilyen típusú RU/s-kiosztott átviteli sebességre, az autoskálázásra és a többrégiós írásokra vonatkozik.  

> [!NOTE]
> A Azure Cosmos DB ingyenes szintje eltér az ingyenes Azure-fióktól. Az ingyenes Azure-fiók korlátozott ideig ingyenesen kínál Azure-krediteket és-erőforrásokat. Ha az ingyenes fiók részeként Azure Cosmos DB használ, a kiosztott átviteli sebesség 12 hónapig 25 GB tárhelyet és 400 RU/s-t kap.

### <a name="billing-example---container-or-database-with-provisioned-throughput"></a>Számlázási példa – tároló vagy adatbázis kiépített átviteli sebességgel
- Tegyük fel, hogy létrehozunk egy adatbázist vagy egy tárolót egy ingyenes szintű fiókban, amely 400 RU/s és 5 GB tárterülettel rendelkezik.
- A számlán nem jelenik meg az erőforrásért fizetendő díj. Óránkénti és havi díja $0 lesz.
- Most tegyük fel, hogy ugyanabban a fiókban egy másik adatbázist vagy tárolót adunk hozzá, 1000 RU/s-t és 10 GB tárterületet.
- A számla mostantól a 1000 RU/s és 10 GB tárterület díját is megjeleníti. 

### <a name="billing-example---container-with-autoscale-throughput"></a>Számlázási példa – tároló az autoscale átviteli sebességgel
- Tegyük fel, hogy egy ingyenes szintű fiókban létrehozunk egy olyan tárolót, amely engedélyezve van, és legfeljebb 4000 RU/s. Ez az erőforrás automatikusan 400 RU/s-4000 RU/s között lesz. 
- Tegyük fel, hogy 1 – óra 10 órában az erőforrás legalább 400 RU/s. A 11. órában az erőforrás akár 1000 RU/s értékre is méretezhető, majd az órán belül 400 RU/s-ra.
- Az 1 – 10 órában az átviteli sebességért $0 díjat számítunk fel, mivel a 400 RU/s-t az ingyenes szintek fedezik. 
- A 11. órában érvényes 1000 RU/s-400 RU/s = 600 RU/s lesz számlázva, mivel ez a legmagasabb RU/s az órában. Ez a 6 egység 100 RU/s lesz az óra számára, így az óra teljes átviteli költsége 6 egység * $0,012 = $0,072 lesz. 
- Minden, az első 5 GB-nál újabb tárterület számlázása normál tárolási díjszabás alapján történik. 

### <a name="billing-example---multi-region-single-write-region-account"></a>Számlázási példa – többrégiós, önálló írási régióbeli fiók
- Tegyük fel, hogy egy ingyenes szintű fiókban létrehozunk egy adatbázist vagy egy tárolót, amely 1200 RU/s és 10 GB tárterülettel rendelkezik. A fiókot 3 régióba replikáljuk, és egyetlen írási régiós fiókkal rendelkezünk.
- Az ingyenes szintek nélkül összesen 3 * 1200 RU/s = 3600 RU/s és 3 * 10 GB = 30 GB tárterület.
- Az ingyenes csomag kedvezménnyel, a 400 RU/s és 5 GB tárterület eltávolítása után a kiépített átviteli sebesség érvényes 3200 RU/s (32 egység) lesz, az egyszeri írási régió díjszabása és 25 GB tárterület.
- Az RU/s havi díja: 32 egység * $0,008 * 24 óra * 31 nap = $190,46. A tárterület havi díja: 25 GB * 0,25/GB = $6,25. A teljes díj $190,46 + $6,25 = $196,71.
- Megjegyzés: Ha a RU/s vagy a Storage egysége eltér a régiókban, az 400 RU/s és az 5 GB-os ingyenes szintet tükrözheti annak a régiónak a díjszabása, amelyben a fiók létrejött.

### <a name="billing-example---multi-region-account-with-multiple-write-regions"></a>Számlázási példa – többrégiós, fiók több írási régióval

Ez a példa a [többrégiós írások díjszabását](https://azure.microsoft.com/pricing/details/cosmos-db/) tükrözi a 2019. december 1. után létrehozott fiókokhoz. 
- Tegyük fel, hogy egy ingyenes szintű fiókban létrehozunk egy adatbázist vagy egy tárolót, amely 1200 RU/s és 10 GB tárterülettel rendelkezik. A fiókot 3 régióba replikáljuk, és több írási régiónk is van. 
- Az ingyenes szintek nélkül összesen 3 * 1200 RU/s = 3600 RU/s és 3 * 10 GB = 30 GB tárterület.
- Az ingyenes szint kedvezménnyel a 400 RU/s és 5 GB tárterület eltávolítása után a rendszer a kiépített átviteli sebességre érvényes 3200 RU/s (32 egység) díjat számítja fel a több írási régióra és 25 GB tárterületre.
- Az RU/s havi díja: 32 egység * $0,016 * 24 óra * 31 nap = $380,93. A tárterület havi díja: 25 GB * 0,25/GB = $6,25. A teljes díj $380,93 + $6,25 = $387,18.

## <a name="proactively-estimating-your-monthly-bill"></a>A havi számla proaktív becslése  

Vegyünk egy másik példát, amelyben a hónap végéig proaktívan kell megbecsülni a számlát. A számlát a következőképpen becsülheti meg:

**Tárolási költség**

* Átlagos rekord mérete (KB) = 1 
* Rekordok száma = 100 000 000 
* Teljes tárterület (GB) = 100 
* Havi költség GB-onként = $0,25 
* A tárolási díj várható havi díja = $25,00 

**Átviteli sebesség**

|Művelettípus| Kérelmek/másodperc| AVG. RU/kérelem| RUs szükséges|
|----|----|----|----|
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
|Összesen|||$8,50|$6 120 |

Vegyük figyelembe, hogy a lefoglalt kapacitást is megvásárolta. Fenntartott kapacitást vásárolhat a 100 – K RU/s számára a $56 064-es díj egy évig (20%-os kedvezménnyel), vagy $6,40/óra áron. A fenntartott kapacitás díjszabása a [díjszabási oldalon](https://azure.microsoft.com/pricing/details/cosmos-db/)található.  

* Átviteli sebesség (utólagos elszámolású): 100 000 RU/s/100 * $0.008/óra * 8760 óra egy évben = $70 080 

* A maximális átviteli sebesség (fenntartott kapacitással) $70 080 20% = $56 064 

A ténylegesen megvásárolt kreditek összege $8 óránként, 100 K/s áron, az USA keleti régiójában, a $6,40/óra díjszabása alapján. Ezt követően az előre fizetett átviteli sebesség foglalása óránként elvégezhető a kiépített átviteli kapacitáshoz bármely globális Azure-régióban az előfizetésre vonatkozó helyi lista díjszabása alapján. Ebben a példában, ahol az USA keleti régiójában és Kelet-Japánban 50 K/s üzembe helyezése történik, a $8,00-os kiépített átviteli sebesség óránként, a díj pedig a $0,50/óra (vagy $360/hó) alapján történik. 

|**Régió**| **Óránkénti ár/100 RU/s**|**Egység (RU/s)**| **Számlázott összeg (óránként)**| **Számlázott összeg (havonta)**|
|----|----|----|----|----|
|USA keleti régiója|$0,008 |50 K|$4|$2 880 |
|Kelet-Japán|$0,009 |50 K| $4,50 |$3 240 |
|||Utólagos elszámolás|$8,50|$6120|
|Lefoglalt kapacitás megvásárlása|$0,0064 (20% kedvezmény) |100 RU/s vagy $8 kapacitás előre megvásárolva |– $8|– $5 760 |
|Nettó számla|||$0.50 |$360 |

## <a name="next-steps"></a>Következő lépések

A következő cikkekkel folytathatja a Azure Cosmos DB a Cost Optimization megismerését:

* További információ arról [, hogy a Cosmos db díjszabási modellje mennyire költséghatékony az ügyfelek számára](total-cost-ownership.md)
* További információ a [fejlesztés és a tesztelés optimalizálásáról](optimize-dev-test.md)
* További információ az [átviteli sebesség optimalizálásáról](optimize-cost-throughput.md)
* További információ a [tárolási díjak optimalizálásáról](optimize-cost-storage.md)
* További információ [az olvasási és írási díjak optimalizálásáról](optimize-cost-reads-writes.md)
* További információ [a lekérdezések díjszabásának optimalizálásáról](./optimize-cost-reads-writes.md)
* További információ [a több régióból álló Azure Cosmos-fiókok díjainak optimalizálásáról](optimize-cost-regions.md)

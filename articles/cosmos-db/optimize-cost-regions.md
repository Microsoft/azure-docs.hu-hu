---
title: A többrégiós üzemelő példányok díjainak optimalizálása Azure Cosmos DB
description: Ez a cikk azt ismerteti, hogyan kezelhető a többrégiós telepítések költségei a Azure Cosmos DBban.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2020
ms.openlocfilehash: a559a51feafa310a4645282dc6368f520fc6b972
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96459623"
---
# <a name="optimize-multi-region-cost-in-azure-cosmos-db"></a>Több régióban való üzemeltetés költségeinek optimalizálása az Azure Cosmos DB-ben
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Bármikor hozzáadhat és eltávolíthat régiókat az Azure Cosmos-fiókjához. A különböző Azure Cosmos-adatbázisokhoz és-tárolóhoz konfigurált átviteli sebesség a fiókhoz társított minden régióban le van foglalva. Ha az óránként kiépített átviteli sebesség az Azure Cosmos-fiók összes adatbázisán és tárolóján konfigurált RU/s összege, `T` valamint az adatbázis-fiókhoz társított Azure-régiók száma `N` , akkor a Cosmos-fiók teljes kiosztott átviteli sebessége egy adott órában egyenlő `T x N RU/s` .

A kiosztott átviteli sebesség költsége egy írási régió esetén: 0,008 USD/óra másodpercenként 100 kérelemegységre vonatkozóan; a kiosztott átviteli sebesség költsége több írható régió esetén: 0,016 USD/óra másodpercenként 100 kérelemegységre vonatkozóan. További információt a Azure Cosmos DB [díjszabását ismertető oldalon](https://azure.microsoft.com/pricing/details/cosmos-db/)talál.

## <a name="costs-for-multiple-write-regions"></a>Több írási régió díja

A többrégiós írások esetében a rendelkezésre álló, írási műveletekhez elérhető net RUs az `N` `N` írási régiók számát is növeli. Az egyrégiós írásokkal ellentétben az egyes régiók már írhatók, és támogatják az ütközés feloldását. A költségmegtakarítás szempontjából, ha `M` ru/s értékre van szükség a globális írásokhoz, az M-t `RUs` egy tárolóban vagy adatbázis szintjén kell kiépíteni. Ezt követően annyi régiót adhat hozzá, amennyit csak szeretne, és ezeket az írásokhoz is felhasználhatja a globális írási műveletek elvégzéséhez `M` .

### <a name="example"></a>Példa

Vegye figyelembe, hogy az USA nyugati régiójában olyan tároló található, amely a 10 000 RU/s kapacitással lett kiépítve, és ezen a hónapban 0,5 TB-nyi adatot tárol. Tegyük fel, hogy hozzáad egy régiót, az USA keleti régióját ugyanazzal a tárterülettel és átviteli sebességgel, és szeretné, hogy az alkalmazásból származó régiókban is írni tudja a tárolókat. Az új teljes havi számla (feltéve, hogy havonta 730 óra) a következő lesz:

|**Elem**|**Használat (havi)**|**Sebesség**|**Havi költség**|
|----|----|----|----|
|Adatátviteli számla az USA nyugati régiójában lévő tárolóhoz (egyetlen írási régió) |10K RU/s * 730 óra |$0,008/100 RU/s óránként |$584 |
|Adatforgalmi számla a 2 régióban található tárolóhoz – USA nyugati régiója & USA keleti régiója (több írási régió) |2 * 10K RU/s * 730 óra |$0,016/100 RU/s óránként |$2 336 |
|Storage-számla az USA nyugati régiójában lévő tárolóhoz |0,5 TB (vagy 512 GB) |0,25/GB |$128 |
|Storage-számla a 2 régióban található tárolóhoz – USA nyugati régiója & USA keleti régiója |2 * 0,5 TB (vagy 1 024 GB) |0,25/GB |$256 |

## <a name="improve-throughput-utilization-on-a-per-region-basis"></a>Az adatforgalom kihasználtságának növelése régiónként-alapon

Ha nem hatékony kihasználtságot használ, például egy vagy több használatban lévő olvasási régiót, akkor hajtsa végre a lépéseket, hogy az olvasási régiókban a hírcsatornák maximálisan használhatók legyenek az olvasási régióból, vagy helyezze át egy másik másodlagosra, ha túlzott kihasználtságú. Először gondoskodnia kell a kiépített átviteli sebesség (RUs) optimalizálásáról az írási régióban. A drágább, mint az olvasási költségeket, kivéve, ha nagyon nagy lekérdezések, így a még kihasználtság fenntartása is kihívást jelenthet. Összességében nyomon követheti a régiókban felhasznált adatátviteli sebességet, és az olvasási és írási sebesség méretezése érdekében igény szerint hozzáadhat vagy eltávolíthat régiókat, így biztos lehet abban, hogy az adott régióban üzembe helyezett alkalmazások késésére is hatással van.

## <a name="next-steps"></a>Következő lépések

A következő cikkekben további tudnivalókat talál a Azure Cosmos DB a Cost optimizationról:

* További információ a [fejlesztés és a tesztelés optimalizálásáról](optimize-dev-test.md)
* További információ [a Azure Cosmos db-számla megismeréséről](understand-your-bill.md)
* További információ az [átviteli sebesség optimalizálásáról](optimize-cost-throughput.md)
* További információ a [tárolási díjak optimalizálásáról](optimize-cost-storage.md)
* További információ [az olvasási és írási díjak optimalizálásáról](optimize-cost-reads-writes.md)
* További információ [a lekérdezések díjszabásának optimalizálásáról](./optimize-cost-reads-writes.md)

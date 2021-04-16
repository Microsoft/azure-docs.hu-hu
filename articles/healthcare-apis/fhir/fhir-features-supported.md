---
title: Támogatott FHIR-funkciók az Azure-ban – Azure API for FHIR
description: Ez a cikk ismerteti, hogy az FHIR-specifikáció mely funkciói vannak megvalósítva a Azure API for FHIR
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 4/15/2021
ms.author: cavoeg
ms.openlocfilehash: 56e3ba46ffb43aec907d729a2e74cdf6f7a62c32
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530631"
---
# <a name="features"></a>Funkciók

Azure API for FHIR a Microsoft FHIR Server for Azure teljes körűen felügyelt üzembe helyezését biztosítja. A kiszolgáló az [FHIR-szabvány implementációja.](https://hl7.org/fhir) Ez a dokumentum az FHIR-kiszolgáló fő funkcióit sorolja fel.

## <a name="fhir-version"></a>FHIR-verzió

A legújabb támogatott verzió: `4.0.1`

A korábbi verziók jelenleg a következőket is támogatják: `3.0.2`

## <a name="rest-api"></a>REST API

| API                            | Támogatott – PaaS | Támogatott – OSS (SQL) | Támogatott – OSS (Cosmos DB) | Megjegyzés                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| olvasás                           | Igen       | Igen       | Igen       |                                                     |
| vread (vread)                          | Igen       | Igen       | Igen       |                                                     |
| update                         | Igen       | Igen       | Igen       |                                                     |
| frissítés optimista zárolással | Igen       | Igen       | Igen       |                                                     |
| frissítés (feltételes)           | Igen       | Igen       | Igen       |                                                     |
| Javítás                          | Nem        | Nem        | Nem        |                                                     |
| delete                         | Igen       | Igen       | Igen       |  Lásd alább a Megjegyzés gombra.                                   |
| törlés (feltételes)           | Nem        | Nem        | Nem        |                                                     |
| előzmények                        | Igen       | Igen       | Igen       |                                                     |
| létrehozás                         | Igen       | Igen       | Igen       | Post/PUT-támogatás                               |
| létrehozás (feltételes)           | Igen       | Igen       | Igen       | Probléma [#1382](https://github.com/microsoft/fhir-server/issues/1382) |
| keresés                         | Részleges   | Részleges   | Részleges   | Lásd alább a Keresés szakaszt.                           |
| láncolt keresés                 | Részleges       | Igen       | Részleges   | Lásd alább a 2. megjegyzést.                                   |
| fordított láncban összefűzve keresés         | Részleges       | Igen       | Részleges   | Lásd alább a 2. megjegyzést.                                   |
| képességek                   | Igen       | Igen       | Igen       |                                                     |
| kötegelt                          | Igen       | Igen       | Igen       |                                                     |
| Tranzakció                    | Nem        | Igen       | Nem        |                                                     |
| Személyhívó                         | Részleges   | Részleges   | Részleges   | `self` és `next` támogatottak                     |
| Közvetítők                 | Nem        | Nem        | Nem        |                                                     |

> [!Note]
> Az FHIR-specifikáció által meghatározott törléshez az szükséges, hogy a törlés után az erőforrás későbbi, nem verzióra vonatkozó olvasásai egy 410-es HTTP-állapotkódot adjanak vissza, és az erőforrás már nem található a keresés során. A Azure API for FHIR lehetővé teszi az erőforrás teljes törlését (az összes előzményt is beleértve). Az erőforrás teljes törléséhez a paraméterbeállításokat át kell adni a `hardDelete` true ( ) `DELETE {server}/{resource}/{id}?hardDelete=true` értéknek. Ha nem adja meg ezt a paramétert, vagy false (hamis) értéket ad meg, az erőforrás korábbi verziói továbbra is `hardDelete` elérhetők lesznek.


 **2. megjegyzés**
* MVP-támogatás hozzáadva a láncolt és fordított láncú FHIR-kereséshez a CosmosDB-ben. 

  A Azure API for FHIR és a Cosmos által háttérként szolgáló nyílt forráskódú FHIR-kiszolgálón a láncolt keresés és a fordított láncolt keresés egy MVP-implementáció. A lekérdezésláncolt keresés Cosmos DB implementációja végigvezeti a keresési kifejezésen, és részlekérdezések segítségével oldja meg az egyező erőforrásokat. Ezt a kifejezés minden szintjén meg kell tenni. Ha bármely lekérdezés több mint 100 eredményt ad vissza, a rendszer hibát jelez. Alapértelmezés szerint a láncolt keresés funkciójelölő mögött van. A láncolt keresés a Cosmos DB használja a `x-ms-enable-chained-search: true` fejlécet. További részletekért lásd: [PR 1695.](https://github.com/microsoft/fhir-server/pull/1695)

## <a name="search"></a>Keresés

Minden keresési paramétertípus támogatott. 

| Keresési paraméter típusa | Támogatott – PaaS | Támogatott – OSS (SQL) | Támogatott – OSS (Cosmos DB) | Megjegyzés |
|-----------------------|-----------|-----------|-----------|---------|
| Szám                | Igen       | Igen       | Igen       |         |
| Dátum/dátum/idő         | Igen       | Igen       | Igen       |         |
| Sztring                | Igen       | Igen       | Igen       |         |
| Jogkivonat                 | Igen       | Igen       | Igen       |         |
| Referencia             | Igen       | Igen       | Igen       |         |
| Kompozit             | Igen       | Igen       | Igen       |         |
| Mennyiség              | Igen       | Igen       | Igen       |         |
| URI                   | Igen       | Igen       | Igen       |         |
| Különleges               | Nem        | Nem        | Nem        |         |


| Módosítók             | Támogatott – PaaS | Támogatott – OSS (SQL) | Támogatott – OSS (Cosmos DB) | Megjegyzés |
|-----------------------|-----------|-----------|-----------|---------|
|`:missing`             | Igen       | Igen       | Igen       |         |
|`:exact`               | Igen       | Igen       | Igen       |         |
|`:contains`            | Igen       | Igen       | Igen       |         |
|`:text`                | Igen       | Igen       | Igen       |         |
|`:[type]` (referencia)  | Igen       | Igen       | Igen       |         |
|`:not`                 | Igen       | Igen       | Igen       |         |
|`:below` (URI)         | Igen       | Igen       | Igen       |         |
|`:above` (URI)         | Nem        | Nem        | Nem        | Probléma [#158](https://github.com/Microsoft/fhir-server/issues/158) |
|`:in` (token)          | Nem        | Nem        | Nem        |         |
|`:below` (token)       | Nem        | Nem        | Nem        |         |
|`:above` (token)       | Nem        | Nem        | Nem        |         |
|`:not-in` (token)      | Nem        | Nem        | Nem        |         |

| Gyakori keresési paraméter | Támogatott – PaaS | Támogatott – OSS (SQL) | Támogatott – OSS (Cosmos DB) | Megjegyzés |
|-------------------------| ----------| ----------| ----------|---------|
| `_id`                   | Igen       | Igen       | Igen       |         |
| `_lastUpdated`          | Igen       | Igen       | Igen       |         |
| `_tag`                  | Igen       | Igen       | Igen       |         |
| `_list`                 | Igen       | Igen       | Igen       |         |
| `_type`                 | Igen       | Igen       | Igen       | Probléma [#1562](https://github.com/microsoft/fhir-server/issues/1562)        |
| `_security`             | Igen       | Igen       | Igen       |         |
| `_profile`              | Részleges   | Részleges   | Részleges   | A STU3 támogatja. Ha 2021. február 20. után hozta létre az adatbázist, az R4-ben is támogatni fogja.  Dolgozunk azon, hogy _profile 2021. február 20. előtt létrehozott adatbázisokon. |
| `_text`                 | Nem        | Nem        | Nem        |         |
| `_content`              | Nem        | Nem        | Nem        |         |
| `_has`                  | Nem        | Nem        | Nem        |         |
| `_query`                | Nem        | Nem        | Nem        |         |
| `_filter`               | Nem        | Nem        | Nem        |         |

| Keresési eredmények paraméterei | Támogatott – PaaS | Támogatott – OSS (SQL) | Támogatott – OSS (Cosmos DB) | Megjegyzés |
|-------------------------|-----------|-----------|-----------|---------|
| `_elements`             | Igen       | Igen       | Igen       | Probléma [#1256](https://github.com/microsoft/fhir-server/issues/1256)        |
| `_count`                | Igen       | Igen       | Igen       | `_count` A legfeljebb 1000 karakter hosszúságú lehet. Ha az 1000-esnél nagyobbra van állítva, a rendszer csak 1000-et ad vissza, és figyelmeztetést ad vissza a csomagban. |
| `_include`              | Igen       | Igen       | Igen       |A benne foglalt elemek száma legfeljebb 100 lehet. A PaaS- és OSS-Cosmos DB nem tartalmazza az :iterate támogatást.|
| `_revinclude`           | Igen       | Igen       | Igen       | A benne foglalt elemek száma legfeljebb 100 lehet. A PaaS- és OSS-Cosmos DB nem [tartalmazza az :iterate támogatást.](https://github.com/microsoft/fhir-server/issues/1313) Probléma [#1319](https://github.com/microsoft/fhir-server/issues/1319)|
| `_summary`              | Részleges   | Részleges   | Részleges   | `_summary=count` támogatott |
| `_total`                | Részleges   | Részleges   | Részleges   | `_total=none` és `_total=accurate`      |
| `_sort`                 | Részleges   | Részleges   | Részleges   |   `_sort=_lastUpdated` támogatott       |
| `_contained`            | Nem        | Nem        | Nem        |         |
| `containedType`         | Nem        | Nem        | Nem        |         |
| `_score`                | Nem        | Nem        | Nem        |         |

## <a name="extended-operations"></a>Kiterjesztett műveletek

A RESTful API kibővítését támogató összes művelet.

| Keresési paraméter típusa | Támogatott – PaaS | Támogatott – OSS (SQL) | Támogatott – OSS (Cosmos DB) | Megjegyzés |
|------------------------|-----------|-----------|-----------|---------|
| $export (teljes rendszer) | Igen       | Igen       | Igen       |         |
| Beteg/$export        | Igen       | Igen       | Igen       |         |
| Csoport/$export          | Igen       | Igen       | Igen       |         |
| $convert-data          | Igen       | Igen       | Igen       |         |


## <a name="persistence"></a>Kitartás

A Microsoft FHIR-kiszolgáló rendelkezik egy moduláris adatmegőrzési modullal (lásd: [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) ).

Az FHIR-kiszolgáló nyílt forráskódja jelenleg tartalmaz egy implementációt a Azure Cosmos DB [és](../../cosmos-db/index-overview.md) [SQL Database.](https://azure.microsoft.com/services/sql-database/)

Cosmos DB egy globálisan elosztott többmodelles (SQL API, MongoDB API stb.) adatbázis. Különböző konzisztenciaszinteket [támogat.](../../cosmos-db/consistency-levels.md) Az alapértelmezett üzembe helyezési sablon konzisztenciával konfigurálja az FHIR-kiszolgálót, de a konzisztencia-szabályzat kérés alapján módosítható (általában enyhül) a kérés `Strong` `x-ms-consistency-level` fejlécének használatával.

## <a name="role-based-access-control"></a>Szerepkör alapú hozzáférés-vezérlés

Az FHIR-kiszolgáló [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) használ a hozzáférés-vezérléshez. Pontosabban a szerepköralapú hozzáférés-vezérlés (RBAC) kényszerítve van, ha a konfigurációs paraméter értéke , és az FHIR-kiszolgálóra vonatkozó összes kérésnek (a kivételével) a kérelemfejlécnek a következőre kell `FhirServer:Security:Enabled` `true` `/metadata` `Authorization` beállítania: `Bearer <TOKEN>` . A jogkivonatnak egy vagy több, a jogcímben meghatározott szerepkört kell `roles` tartalmaznia. A rendszer akkor engedélyezi a kérést, ha a jogkivonat olyan szerepkört tartalmaz, amely engedélyezi a megadott műveletet a megadott erőforráson.

Egy adott szerepkör engedélyezett műveletei jelenleg globálisan vannak *alkalmazva az* API-n.

## <a name="service-limits"></a>Szolgáltatási korlátozások

* [**Kérelemegységek (RU-k)**](../../cosmos-db/concepts-limits.md) – A portálon legfeljebb 10 000 kérelemegységet konfigurálhat a Azure API for FHIR. Legalább 400 ru-ra vagy 40 RU/GB-ra lesz szüksége, amelyik nagyobb. Ha több mint 10 000 ru-ra van szüksége, támogatási jegyet is be lehet tenni a növekedéshez. A maximálisan elérhető érték 1 000 000.

* **Egyidejű** kapcsolatok **és** példányok – Alapértelmezés szerint öt egyidejű kapcsolattal rendelkezik a fürt két példányán (összesen 10 egyidejű kéréshez). Ha úgy véli, hogy több egyidejű kérésre van szüksége, nyisson egy támogatási jegyet, amely tartalmazza az igényeinek részleteit.

* **Csomagméret** – Minden csomag legfeljebb 500 elemet tartalmaz.

* **Adatok mérete** – Az adatoknak/dokumentumoknak valamivel kisebbnek kell lennie 2 MB-nál.

## <a name="performance-expectations"></a>Teljesítménnyel kapcsolatos elvárások

A rendszer teljesítménye a ru-k számától, az egyidejű kapcsolatok számától és a végrehajtani (Put, Post stb.) végrehajtani szükséges műveletek típusától függ. Az alábbiakban a konfigurált ru-k alapján várható általános tartományokat olvashatja. Általánosságban elmondható, hogy a teljesítmény lineárisan skálázható a ru-k számának növekedésével:

| A ru-k (RU-k) | Erőforrások másodpercenként |    Maximális tárterület (GB)*    |
|----------|---------------|--------|                 
| 400      | 5-10          |     10   |
| 1,000    | 100-150       |      25  |
| 10,000   | 225-400       |      250  |
| 100.000  | 2,500-4,000   |      2500  |

Megjegyzés: Cosmos DB követelmény, hogy legalább 40 RU/s átviteli sebességet kell kibocsátani GB-ban. 

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben az FHIR támogatott funkcióiról olvashatott a Azure API for FHIR. Ezután telepítse a Azure API for FHIR.
 
>[!div class="nextstepaction"]
>[Az Azure API for FHIR üzembe helyezése](fhir-paas-portal-quickstart.md)

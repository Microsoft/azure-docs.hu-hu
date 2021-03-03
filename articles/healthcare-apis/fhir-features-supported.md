---
title: Támogatott FHIR-funkciók az Azure-ban – Azure API a FHIR-hez
description: Ez a cikk ismerteti a FHIR-specifikáció azon funkcióit, amelyek a FHIR készült Azure API-ban lettek implementálva
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 1/30/2021
ms.author: cavoeg
ms.openlocfilehash: 19f051320aaa675ebe5ff148fb6580c2a5d8770c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101719134"
---
# <a name="features"></a>Funkciók

A FHIR készült Azure API teljes körűen felügyelt üzembe helyezést biztosít az Azure-hoz készült Microsoft FHIR-kiszolgáló számára. A kiszolgáló a [FHIR](https://hl7.org/fhir) standard implementációja. Ez a dokumentum a FHIR-kiszolgáló fő funkcióit sorolja fel.

## <a name="fhir-version"></a>FHIR verziója

A legújabb verzió támogatott: `4.0.1`

A korábbi verziók jelenleg is támogatottak: `3.0.2`

## <a name="rest-api"></a>REST API

| API                            | Támogatott – Péter | Támogatott-OSS (SQL) | Támogatott-OSS (Cosmos DB) | Megjegyzés                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| olvasás                           | Igen       | Igen       | Igen       |                                                     |
| vread                          | Igen       | Igen       | Igen       |                                                     |
| update                         | Igen       | Igen       | Igen       |                                                     |
| frissítés optimista zárolással | Igen       | Igen       | Igen       |                                                     |
| frissítés (feltételes)           | Igen       | Igen       | Igen       |                                                     |
| javítás                          | Nem        | Nem        | Nem        |                                                     |
| delete                         | Igen       | Igen       | Igen       |  Lásd az alábbi megjegyzést                                                   |
| Törlés (feltételes)           | Nem        | Nem        | Nem        |                                                     |
| előzmények                        | Igen       | Igen       | Igen       |                                                     |
| létrehozás                         | Igen       | Igen       | Igen       | Mind a POST, mind a PUT támogatása                               |
| létrehozás (feltételes)           | Igen       | Igen       | Igen       | Probléma [#1382](https://github.com/microsoft/fhir-server/issues/1382) |
| keresés                         | Részleges   | Részleges   | Részleges   | Lásd lent                                           |
| láncolt keresés                 | Nem        | Igen       | Nem        |                                                     |
| fordított láncolt keresés         | Nem        | Igen       | Nem        |                                                     |
| képességek                   | Igen       | Igen       | Igen       |                                                     |
| kötegelt                          | Igen       | Igen       | Igen       |                                                     |
| tranzakció                    | Nem        | Igen       | Nem        |                                                     |
| lapozófájl                         | Részleges   | Részleges   | Részleges   | `self` és `next` támogatottak                     |
| közvetítők                 | Nem        | Nem        | Nem        |                                                     |

> [!Note]
> A FHIR specifikáció által definiált törléshez a törlés után az erőforrás későbbi, nem adott olvasási művelete egy 410 HTTP-állapotkódot ad vissza, és az erőforrás már nem található meg a keresésen keresztül. A FHIR készült Azure API lehetővé teszi az erőforrás teljes törlését (az összes előzményt is beleértve). Az erőforrás teljes törléséhez átadhatja a paraméter beállításait `hardDelete` true () értékre `DELETE {server}/{resource}/{id}?hardDelete=true` . Ha nem adja át ezt a paramétert, vagy hamis értékre állítja, akkor az `hardDelete` erőforrás korábbi verziói továbbra is elérhetők lesznek.

## <a name="search"></a>Keresés

Az összes keresési paraméter típusa támogatott. 

| Keresési paraméter típusa | Támogatott – Péter | Támogatott-OSS (SQL) | Támogatott-OSS (Cosmos DB) | Megjegyzés |
|-----------------------|-----------|-----------|-----------|---------|
| Szám                | Igen       | Igen       | Igen       |         |
| Dátum/dátum/idő         | Igen       | Igen       | Igen       |         |
| Sztring                | Igen       | Igen       | Igen       |         |
| Jogkivonat                 | Igen       | Igen       | Igen       |         |
| Referencia             | Igen       | Igen       | Igen       |         |
| Kompozit             | Igen       | Igen       | Igen       |         |
| Mennyiség              | Igen       | Igen       | Igen       |         |
| URI                   | Igen       | Igen       | Igen       |         |
| Speciális               | Nem        | Nem        | Nem        |         |


| Módosítók             | Támogatott – Péter | Támogatott-OSS (SQL) | Támogatott-OSS (Cosmos DB) | Megjegyzés |
|-----------------------|-----------|-----------|-----------|---------|
|`:missing`             | Igen       | Igen       | Igen       |         |
|`:exact`               | Igen       | Igen       | Igen       |         |
|`:contains`            | Igen       | Igen       | Igen       |         |
|`:text`                | Igen       | Igen       | Igen       |         |
|`:[type]` referencia  | Igen       | Igen       | Igen       |         |
|`:not`                 | Igen       | Igen       | Igen       |         |
|`:below` URI         | Igen       | Igen       | Igen       |         |
|`:above` URI         | Nem        | Nem        | Nem        | Probléma [#158](https://github.com/Microsoft/fhir-server/issues/158) |
|`:in` jogkivonat          | Nem        | Nem        | Nem        |         |
|`:below` jogkivonat       | Nem        | Nem        | Nem        |         |
|`:above` jogkivonat       | Nem        | Nem        | Nem        |         |
|`:not-in` jogkivonat      | Nem        | Nem        | Nem        |         |

| Gyakori keresési paraméter | Támogatott – Péter | Támogatott-OSS (SQL) | Támogatott-OSS (Cosmos DB) | Megjegyzés |
|-------------------------| ----------| ----------| ----------|---------|
| `_id`                   | Igen       | Igen       | Igen       |         |
| `_lastUpdated`          | Igen       | Igen       | Igen       |         |
| `_tag`                  | Igen       | Igen       | Igen       |         |
| `_list`                 | Igen       | Igen       | Igen       |         |
| `_type`                 | Igen       | Igen       | Igen       | Probléma [#1562](https://github.com/microsoft/fhir-server/issues/1562)        |
| `_security`             | Igen       | Igen       | Igen       |         |
| `_profile`              | Részleges   | Részleges   | Részleges   | A STU3 támogatja. Ha az adatbázist 2021 február 20. **után** hozta létre, akkor az R4-t is támogatja. Azon dolgozunk, hogy engedélyezzük a _profile a 2021. február 20. előtt létrehozott adatbázisokon. |
| `_text`                 | Nem        | Nem        | Nem        |         |
| `_content`              | Nem        | Nem        | Nem        |         |
| `_has`                  | Nem        | Nem        | Nem        |         |
| `_query`                | Nem        | Nem        | Nem        |         |
| `_filter`               | Nem        | Nem        | Nem        |         |

| Keresési eredmények paraméterei | Támogatott – Péter | Támogatott-OSS (SQL) | Támogatott-OSS (Cosmos DB) | Megjegyzés |
|-------------------------|-----------|-----------|-----------|---------|
| `_elements`             | Igen       | Igen       | Igen       | Probléma [#1256](https://github.com/microsoft/fhir-server/issues/1256)        |
| `_count`                | Igen       | Igen       | Igen       | `_count` legfeljebb 1000 karakter hosszú lehet. Ha 1000-nél magasabbra van állítva, akkor a rendszer csak 1000 értéket ad vissza, és a kötegben figyelmeztetést ad vissza. |
| `_include`              | Igen       | Igen       | Igen       |A tartalmazott elemek 100-re korlátozódnak. A (z) Cosmos DB nem tartalmazza a (z) és az OSS-t a következőn: iteráció támogatása.|
| `_revinclude`           | Igen       | Igen       | Igen       | A tartalmazott elemek 100-re korlátozódnak. A (z) Cosmos DB nem tartalmazza a (z) és az OSS-t a következőn [: iteráció támogatása](https://github.com/microsoft/fhir-server/issues/1313). Probléma [#1319](https://github.com/microsoft/fhir-server/issues/1319)|
| `_summary`              | Részleges   | Részleges   | Részleges   | `_summary=count` támogatott |
| `_total`                | Részleges   | Részleges   | Részleges   | `_total=none` és `_total=accurate`      |
| `_sort`                 | Részleges   | Részleges   | Részleges   |   `_sort=_lastUpdated` támogatott       |
| `_contained`            | Nem        | Nem        | Nem        |         |
| `containedType`         | Nem        | Nem        | Nem        |         |
| `_score`                | Nem        | Nem        | Nem        |         |

## <a name="extended-operations"></a>Kiterjesztett műveletek

A REST API-t kiterjesztő összes támogatott művelet.

| Keresési paraméter típusa | Támogatott – Péter | Támogatott-OSS (SQL) | Támogatott-OSS (Cosmos DB) | Megjegyzés |
|------------------------|-----------|-----------|-----------|---------|
| $export (teljes rendszeren) | Igen       | Igen       | Igen       |         |
| Beteg/$export        | Igen       | Igen       | Igen       |         |
| Csoport/$export          | Igen       | Igen       | Igen       |         |
| $convert – adatkezelés          | Igen       | Igen       | Igen       |         |


## <a name="persistence"></a>Kitartás

A Microsoft FHIR-kiszolgáló rendelkezik egy csatlakoztatható adatmegőrzési modullal (lásd: [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) ).

Jelenleg a FHIR-kiszolgáló nyílt forráskódú programkódja [Azure Cosmos db](../cosmos-db/index-overview.md) és [SQL Database](https://azure.microsoft.com/services/sql-database/)megvalósítását is tartalmazza.

A Cosmos DB egy globálisan elosztott, többmodelles (SQL API-, MongoDB API-stb.-) adatbázis. Különböző konzisztencia- [szinteket](../cosmos-db/consistency-levels.md)támogat. Az alapértelmezett központi telepítési sablon konzisztens FHIR-kiszolgálót konfigurál `Strong` , de a konzisztencia-házirend a kérelem fejlécének használatával a kérelem alapján módosítható (általában nyugodt) `x-ms-consistency-level` .

## <a name="role-based-access-control"></a>Szerepkör alapú hozzáférés-vezérlés

A FHIR-kiszolgáló [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) használ a hozzáférés-vezérléshez. A szerepköralapú hozzáférés-vezérlés (RBAC) kényszerítve van, ha a `FhirServer:Security:Enabled` konfigurációs paraméter értéke `true` , és a FHIR-kiszolgálónak küldött összes kérelemnek (kivéve `/metadata` ) a `Authorization` kérelem fejlécét kell beállítani `Bearer <TOKEN>` . A tokennek tartalmaznia kell egy vagy több, a jogcímben definiált szerepkört `roles` . A rendszer akkor fogadja a kérést, ha a jogkivonat olyan szerepkört tartalmaz, amely engedélyezi a megadott műveletet a megadott erőforráson.

Jelenleg az adott szerepkörre vonatkozó engedélyezett műveletek *globálisan* lesznek alkalmazva az API-ra.

## <a name="service-limits"></a>Szolgáltatási korlátozások

* [**Kérelmek egységei (RUS)**](../cosmos-db/concepts-limits.md) – a FHIR-hez készült Azure API-portálon akár 10 000 RUs is konfigurálható. Legalább 400 RUs vagy 10 RUs/GB szükséges, attól függően, hogy melyik a nagyobb. Ha 10 000 RUs-nál többre van szüksége, a megnövelt támogatási jegybe helyezhető. Az elérhető maximális érték 1 000 000.

* **Egyidejű kapcsolatok** és **példányok** – alapértelmezés szerint öt egyidejű kapcsolat van a fürt két példányán (összesen 10 egyidejű kérelem esetén). Ha úgy gondolja, hogy több egyidejű kérésre van szüksége, nyisson meg egy támogatási jegyet az igényeinek megfelelő részletekkel.

* **Köteg mérete** – minden köteg 500 elemre van korlátozva.

* **Adatméret** – az adatmennyiségnek és a dokumentumoknak egyenként 2 MB-nál kisebbnek kell lenniük.

## <a name="performance-expectations"></a>Teljesítményre vonatkozó elvárások

A rendszer teljesítménye az RUs, az egyidejű kapcsolatok és a végrehajtott műveletek (Put, post stb.) számától függ. Az alábbiakban a konfigurált RUs alapján várhatóan megjelenő általános tartományok láthatók. Általánosságban elmondható, hogy a teljesítmény lineárisan növekszik az RUs növekedésével:

| RUs száma | Erőforrások/mp |    Maximális tárterület (GB) *    |
|----------|---------------|--------|                 
| 400      | 5-10          |     40   |
| 1,000    | 100-150       |      100  |
| 10,000   | 225-400       |      1,000  |
| 100.000  | 2500 – 4000   |      10,000  |

Megjegyzés: Cosmos DB követelmény szerint GB-nál kevesebb tárterületre van szükség a legalább 10 RU/s teljesítményre. További információkért tekintse meg [Cosmos db szolgáltatási kvótákat](../cosmos-db/concepts-limits.md).

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben a FHIR készült Azure API támogatott FHIR szolgáltatásairól olvashat. Ezután telepítse az Azure API-t a FHIR-hez.
 
>[!div class="nextstepaction"]
>[Az Azure API for FHIR üzembe helyezése](fhir-paas-portal-quickstart.md)

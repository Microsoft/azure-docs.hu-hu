---
title: Egyéni lekérdezési specifikáció
description: Ismerje meg, hogyan használhatja az egyéni lekérdezéseket, hogy programozott módon kinyerje az ajánlatokat a Microsoft kereskedelmi piactéren az ajánlatok elemzési tábláiból.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 4be063342a6c46d73c86f2d9dff1da5395328389
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102583832"
---
# <a name="custom-query-specification"></a>Egyéni lekérdezési specifikáció

A partnerek ezt a lekérdezési specifikációt használva könnyedén készíthetnek egyéni lekérdezéseket az adatok elemzési táblákból való kinyeréséhez. A lekérdezések segítségével csak a kívánt oszlopokat és mérőszámokat választhatja ki, amelyek megfelelnek egy adott feltételnek. A nyelv specifikációjának középpontjában az adatkészlet definíciója látható, amelyen az egyéni lekérdezés írható.

## <a name="datasets"></a>Adathalmazok

Ugyanúgy, ahogy egyes lekérdezések olyan adatbázison futnak, amely táblákat és oszlopokat tartalmaz, egy egyéni lekérdezés olyan adatkészleteken működik, amelyek oszlopokkal és metrikákkal rendelkeznek. A lekérdezések összeállításához rendelkezésre álló adatkészletek teljes listája az adatkészletek API használatával szerezhető be.

Íme egy példa a JSON-ként megjelenő adatkészletre.

```json
{
     "datasetName": "ISVUsage",
     "selectableColumns": [
       "MarketplaceSubscriptionId",
       "OfferName",
            "CustomerId",
     "MonthStartDate",
     "SKU"
    ],
    "availableMetrics": [
     "NormalizedUsage", 
     "RawUsage"
        "EstimatedExtendedChargeCC"
    ],
    "availableDateRanges": [
     "LAST_MONTH",
     "LAST_3_MONTHS",
     "LAST_6_MONTHS",
     "LIFETIME"
    ]
}
```

### <a name="parts-of-a-dataset"></a>Adatkészlet részei

- Az adatkészlet neve például egy adatbázis-táblázat neve. Például: ISVUsage. Az adatkészlet a kiválasztható oszlopok listáját tartalmazza, például MarketplaceSubscriptionId.
- Az adatkészlet olyan mérőszámokkal is rendelkezik, amelyek egy adatbázis aggregációs függvényeit hasonlítják össze. Például: NormalizedUsage.
- Vannak rögzített időkeretek, amelyekben az adatexportálás lehetséges.

### <a name="formulating-a-query-on-a-dataset"></a>Lekérdezés készítése egy adatkészleten

Íme néhány példa a különböző típusú adatok kinyerését bemutató példákra.

| Lekérdezés | Leírás |
| ------------ | ------------- |
| **Válassza ki** MarketplaceSubscriptionId, Vevőkód **a** ISVUsage **TIMESPAN LAST_MONTH** | Ez a lekérdezés minden egyedi `MarketplaceSubscriptionId` és az `CustomerId` utolsó 1 hónapban jelenik meg. |
| **Válassza ki** MarketplaceSubscriptionId, EstimatedExtendedChargeCC **a** ISVUSAGE **Order by** EstimatedExtendedChargeCC **limit** 10 | Ez a lekérdezés az egyes előfizetésekben eladott licencek számának csökkenő sorrendjében kapja meg az első 10 előfizetést. |
| **Válassza ki** Vevőkód, NormalizedUsage, RawUsage **a** ISVUSAGE **Order by** NormalizedUsage, **ahol** NormalizedUsage > 100000 **Order by** NormalizedUsage **TIMESPAN** LAST_6_MONTHS | Ez a lekérdezés a 100 000-nál nagyobb NormalizedUsage rendelkező ügyfelek NormalizedUsage és RawUsage fogja lekérni. |
| **Válassza ki** MarketplaceSubscriptionId, MonthStartDate, NormalizedUsage **a** ISVUsage, **ahol** a Vevőkód a (z) ("2a31c234-1f4e-4c60-909e-76d234f93161", "80780748-3f9a-11eb-b378-0242ac130002") | A lekérdezés a `MarketplaceSubscriptionId` következő két értékkel kapja meg a és a általa generált bevételt `CustomerId` : `2a31c234-1f4e-4c60-909e-76d234f93161` és `80780748-3f9a-11eb-b378-0242ac130002` . |
|||

## <a name="query-specification"></a>Lekérdezés specifikációja

Ez a szakasz a lekérdezés definícióját és szerkezetét ismerteti.

### <a name="grammar-reference"></a>Nyelvtani útmutató

Ez a táblázat a lekérdezésekben használt szimbólumokat ismerteti.

| Szimbólum | Értelmezés |
| ------------ | ------------- |
| ? | Választható |
| * | Nulla vagy több |
| + | Egy vagy több |
| &#124; | Vagy a lista egyike |
| | |

### <a name="query-definition"></a>Lekérdezés definíciója

A lekérdezési utasításhoz a következő záradékok érvényesek: SelectClause, FromClause, WhereClause?, OrderClause?, LimitClause? és TimeSpan?.

- **SelectClause**: **Select** ColumOrMetricName (, ColumOrMetricName) *
    - **ColumOrMetricName**: az adatkészleten belül definiált oszlopok és mérőszámok
- **FromClause**: **a** datasetname tulajdonságának értéke
    - **Datasetname tulajdonságának értéke**: az adatkészleten belül definiált adatkészlet neve
- **WhereClause**: **Where** FilterCondition (**és** FilterCondition) *
    - **FilterCondition**: ColumOrMetricName operátor értéke
        - **Operátor**: = | > | < | >= | <= |! = | PÉLDÁUL | NEM HASONLÓ | A | NEM A
        - **Érték**: Number | StringLiteral | MultiNumberList | MultiStringList 
            - **Szám**:-? [0 – 9] + (. [0-9] [0-9] *)?
            - **StringLiteral**: ' [a-Za-z0-9_] * '
            - **MultiNumberList**: (szám (, szám) *)
            - **MultiStringList**: (StringLiteral (, StringLiteral) *)
- **OrderClause**: **Order by** OrderCondition (, OrderCondition) *
    - **OrderCondition**: ColumOrMetricName (**Asc**  |  **desc**) *
    - **LimitClause**: **korlát** [0-9] +
    - **TimeSpan**: **TimeSpan** (ma | TEGNAP | LAST_7_DAYS | LAST_14_DAYS | LAST_30_DAYS | LAST_90_DAYS | LAST_180_DAYS | LAST_365_DAYS | LAST_MONTH | LAST_3_MONTHS | LAST_6_MONTHS | LAST_1_YEAR | ÉLETTARTAMA

### <a name="query-structure"></a>Lekérdezés szerkezete

Egy jelentés lekérdezése több részből áll:

- SELECT
- FROM
- WHERE
- ORDER BY
- KORLÁTOT
- TIMESPAN

Az egyes részekről alább olvashat.

#### <a name="select"></a>SELECT

A lekérdezés ezen része az exportálandó oszlopokat határozza meg. A kiválasztható oszlopok az `selectableColumns` `availableMetrics` adathalmazok és az adatkészletek részeiben felsorolt mezők. A végleges exportált sorok mindig külön értékeket tartalmaznak a kijelölt oszlopokban. Az exportált fájlban például nem lesznek ismétlődő sorok. A metrikák a kijelölt oszlopok minden különböző kombinációjára kiszámítva lesznek.

**Példa**:
- **Válassza a lehetőséget** `OfferName` , `NormalizedUsage`

#### <a name="from"></a>FROM

A lekérdezés ezen része azt az adatkészletet jelzi, amelyből exportálni kell az adatokat. Az itt megadott adatkészlet nevének érvényes adatkészlet-névnek kell lennie, amelyet az adatkészletek API-nak ad vissza.

**Példa**:
- A `ISVUsage`
- A `ISVOrder`

#### <a name="where"></a>WHERE

A lekérdezés ezen része a szűrési feltételek megadására szolgál az adatkészleten. A végleges exportált fájlban csak a záradékban felsorolt összes feltételnek megfelelő sorok jelennek meg. A szűrési feltétel a és a listájában felsorolt oszlopok bármelyikén lehet `selectableColumns` `availableMetrics` . A szűrési feltételben megadott értékek lehetnek számok, vagy csak akkor, ha az operátor vagy a sztringek listája `IN` `NOT IN` . Az értékeket mindig literál sztringként lehet megadni, és a rendszer az oszlopok natív típusaira konvertálja őket. Több szűrési feltételnek kell elkülöníteni egy `AND` művelettel.

**Példa**:

- MarketplaceSubscriptionId = ' 868368da-957d-4959-8992-3c12dc7e6260 '
- Customername (, **mint** a (z)% Contosso%
- **A Vevőkód nem a** (1000, 1001, 1002)
- OrderQuantity = 100
- OrderQuantity = ' 100 '
    - MarketplaceSubscriptionId = ' 7b487ac0-ce12-b732-dcd6-91a1e4e74a50 ' és Vevőkód = ' 0f8b7fa0-eb83-a183-1225-ca153ef807aa '

#### <a name="order-by"></a>ORDER BY

A lekérdezés ezen része az exportált sorok rendezési feltételeit határozza meg. Azok az oszlopok, amelyeken a rendezés definiálható, az `selectableColumns` `availableMetrics` adatkészletből kell származnia. Ha nincs megadva a megrendelés iránya, a rendszer alapértelmezés szerint `DESC` az oszlopba kerül. A rendezés több oszlopon is meghatározható a feltételek vesszővel való elválasztásával.

**Példa**:

- **rendezési sorrend** NormalizedUsage **Asc**, ESTIMATEDEXTENDEDCHARGE (CC) **desc**
- **rendezési sorrend** Customername ( **Asc**, NormalizedUsage

#### <a name="limit"></a>KORLÁTOT

A lekérdezés ezen része az exportálandó sorok számát határozza meg. A megadott számnak pozitív, nem nulla egész számnak kell lennie.

#### <a name="timespan"></a>TIMESPAN

A lekérdezés ezen része meghatározza azt az időtartamot, ameddig az adatexportálást el kell érni. A lehetséges értékek az `availableDateRanges` adatkészlet definíciójának mezőjéből származnak.

### <a name="case-sensitivity-in-query-specification"></a>Kis-és nagybetűk megkülönböztetése a lekérdezés specifikációjában

A specifikáció teljesen megkülönbözteti a kis-és nagybetűket. Az előre definiált kulcsszavak, oszlopnevek és értékek a kis-és nagybetűkkel is megadhatók.

## <a name="see-also"></a>Lásd még

- [Rendszerlekérdezések listája](analytics-system-queries.md)

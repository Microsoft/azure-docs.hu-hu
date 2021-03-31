---
title: Átalakítás összeolvasztása a leképezési adatfolyamban
description: Hierarchikus adatai denormalizálása a összeolvasztási transzformáció használatával
author: kromerm
ms.author: makromer
ms.review: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: a0e75957a0ab49394dab56f2b7fb847dee4b43cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "81413681"
---
# <a name="flatten-transformation-in-mapping-data-flow"></a>Átalakítás összeolvasztása a leképezési adatfolyamban

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A összeolvasztási transzformáció használatával a tömb értékeit hierarchikus struktúrákba, például JSON-ba helyezheti el, és lefordíthatja őket különálló sorokba. Ez a folyamat denormalizálás néven ismert.

## <a name="configuration"></a>Konfiguráció

A összeolvasztási transzformáció a következő konfigurációs beállításokat tartalmazza

![Beállítások összeolvasztása](media/data-flow/flatten1.png "Beállítások összeolvasztása")

### <a name="unroll-by"></a>Legördülő

Válasszon ki egy tömböt a legördülő listából. A kimeneti adatokat minden tömbben egy sor fogja tartalmazni. Ha a bemeneti sorban lévő unroll by tömb null értékű vagy üres, akkor egyetlen kimeneti sor lesz NULL értékként megadva.

### <a name="unroll-root"></a>Legördülő gyökér

Alapértelmezés szerint a összeolvasztási transzformáció leállítja a tömböt a-hierarchia tetejére. Kiválaszthat egy tömböt a legördülő gyökérként. A legördülő főtanúsítványnak olyan összetett objektumok tömbje kell, amelyek vagy a unroll by tömböt tartalmazzák. Ha a legördülő főtanúsítvány ki van választva, a kimeneti adatok legalább egy sort tartalmaznak a legördülő főtanúsítványban. Ha a bemeneti sorban nincsenek elemek a legördülő főgyökérben, a rendszer elveti a kimeneti adatokból. A legördülő főtanúsítványok kiválasztásakor a rendszer mindig kisebb vagy egyenlő számú sort ad eredményül, mint az alapértelmezett viselkedés.

### <a name="flatten-mapping"></a>Lelapul leképezés

A Select transzformációhoz hasonlóan válassza ki az új struktúra kivetítését a bejövő mezőkből és a denormalizált tömbből. Ha egy denormalizált tömb le van képezve, a kimeneti oszlop a tömb adattípusa lesz. Ha az unroll by tömb olyan összetett objektumok tömbje, amelyek altömböket tartalmaznak, az adott subarry egy elemének leképezése kimenetként egy tömböt fog kiszolgálni.

A leképezési kimenet ellenőrzéséhez tekintse meg a vizsgálat lapot és az adatelőnézett.

## <a name="examples"></a>Példák

Tekintse meg a következő JSON-objektumot az alábbi példákban a lelapul átalakításhoz

``` json
{
  "name":"MSFT","location":"Redmond", "satellites": ["Bay Area", "Shanghai"],
  "goods": {
    "trade":true, "customers":["government", "distributer", "retail"],
    "orders":[
        {"orderId":1,"orderTotal":123.34,"shipped":{"orderItems":[{"itemName":"Laptop","itemQty":20},{"itemName":"Charger","itemQty":2}]}},
        {"orderId":2,"orderTotal":323.34,"shipped":{"orderItems":[{"itemName":"Mice","itemQty":2},{"itemName":"Keyboard","itemQty":1}]}}
    ]}}
{"name":"Company1","location":"Seattle", "satellites": ["New York"],
  "goods":{"trade":false, "customers":["store1", "store2"],
  "orders":[
      {"orderId":4,"orderTotal":123.34,"shipped":{"orderItems":[{"itemName":"Laptop","itemQty":20},{"itemName":"Charger","itemQty":3}]}},
      {"orderId":5,"orderTotal":343.24,"shipped":{"orderItems":[{"itemName":"Chair","itemQty":4},{"itemName":"Lamp","itemQty":2}]}}
    ]}}
{"name": "Company2", "location": "Bellevue",
  "goods": {"trade": true, "customers":["Bank"], "orders": [{"orderId": 4, "orderTotal": 123.34}]}}
{"name": "Company3", "location": "Kirkland"}
```

### <a name="no-unroll-root-with-string-array"></a>Nincs legördülő gyökér karakterlánc-tömbvel

| Legördülő | Legördülő gyökér | Vetület |
| --------- | ----------- | ---------- |
| termékek. ügyfelek | Nincsenek | name <br> ügyfél = termékek. ügyfél |

#### <a name="output"></a>Kimenet

```
{ 'MSFT', 'government'}
{ 'MSFT', 'distributer'}
{ 'MSFT', 'retail'}
{ 'Company1', 'store'}
{ 'Company1', 'store2'}
{ 'Company2', 'Bank'}
{ 'Company3', null}
```

### <a name="no-unroll-root-with-complex-array"></a>Nincs legördülő gyökér összetett tömbvel

| Legördülő | Legördülő gyökér | Vetület |
| --------- | ----------- | ---------- |
| áruk. Orders. szállított. orderItems | Nincsenek | name <br> Rendeléskód = termékek. Orders. Rendeléskód <br> itemName = termékek. Orders. szállított. orderItems. itemName <br> itemQty = termékek. Orders. szállított. orderItems. itemQty <br> hely = hely |

#### <a name="output"></a>Kimenet

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'}
{ 'MSFT', 1, 'Charger', 2, 'Redmond'}
{ 'MSFT', 2, 'Mice', 2, 'Redmond'}
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'}
{ 'Company1', 4, 'Laptop', 20, 'Seattle'}
{ 'Company1', 4, 'Charger', 3, 'Seattle'}
{ 'Company1', 5, 'Chair', 4, 'Seattle'}
{ 'Company1', 5, 'Lamp', 2, 'Seattle'}
{ 'Company2', 4, null, null, 'Bellevue'}
{ 'Company3', null, null, null, 'Kirkland'}
```

### <a name="same-root-as-unroll-array"></a>Ugyanaz a gyökér, mint a legördülő tömb

| Legördülő | Legördülő gyökér | Vetület |
| --------- | ----------- | ---------- |
| termékek. megrendelések | termékek. megrendelések | name <br> áruk. Orders. szállított. orderItems. itemName <br> termékek. ügyfelek <br> location |

#### <a name="output"></a>Kimenet

```
{ 'MSFT', ['Laptop','Charger'], ['government','distributer','retail'], 'Redmond'}
{ 'MSFT', ['Mice', 'Keyboard'], ['government','distributer','retail'], 'Redmond'}
{ 'Company1', ['Laptop','Charger'], ['store', 'store2'], 'Seattle'}
{ 'Company1', ['Chair', 'Lamp'], ['store', 'store2'], 'Seattle'}
{ 'Company2', null, ['Bank'], 'Bellevue'}
```

### <a name="unroll-root-with-complex-array"></a>Gyökér kivonása összetett tömbvel

| Legördülő | Legördülő gyökér | Vetület |
| --------- | ----------- | ---------- |
| áruk. Orders. szállított. orderItem | termékek. megrendelések |name <br> Rendeléskód = termékek. Orders. Rendeléskód <br> itemName = termékek. Orders. szállított. orderItems. itemName <br> itemQty = termékek. Orders. szállított. orderItems. itemQty <br> hely = hely |

#### <a name="output"></a>Kimenet

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'}
{ 'MSFT', 1, 'Charger', 2, 'Redmond'}
{ 'MSFT', 2, 'Mice', 2, 'Redmond'}
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'}
{ 'Company1', 4, 'Laptop', 20, 'Seattle'}
{ 'Company1', 4, 'Charger', 3, 'Seattle'}
{ 'Company1', 5, 'Chair', 4, 'Seattle'}
{ 'Company1', 5, 'Lamp', 2, 'Seattle'}
{ 'Company2', 4, null, null, 'Bellevue'}
```

## <a name="data-flow-script"></a>Adatfolyamszkript

### <a name="syntax"></a>Syntax

```
<incomingStream>
foldDown(unroll(<unroll cols>),
    mapColumn(
        name,
        each(<array>(type == '<arrayDataType>')),
        each(<array>, match(true())),
        location
    )) ~> <transformationName>
```

### <a name="example"></a>Példa

```
source foldDown(unroll(goods.orders.shipped.orderItems, goods.orders),
    mapColumn(
        name,
        orderId = goods.orders.orderId,
        itemName = goods.orders.shipped.orderItems.itemName,
        itemQty = goods.orders.shipped.orderItems.itemQty,
        location = location
    ),
    skipDuplicateMapInputs: false,
    skipDuplicateMapOutputs: false) 
```    

## <a name="next-steps"></a>Következő lépések

* Sorok oszlopokra való kimutatásához használja a [pivot transzformációt](data-flow-pivot.md) .
* A kimutatás [transzformációval](data-flow-unpivot.md) oszlopokat szúrhat be a sorokba.

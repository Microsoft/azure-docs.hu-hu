---
title: StylesObject séma-útmutató a dinamikus Azure Maps
description: Útmutató a dinamikus Azure Maps StylesObject sémájának és szintaxisának áttekintéséhez.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: reference
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 08379e66c97d34eea53410190475e90e156a58e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96903343"
---
# <a name="stylesobject-schema-reference-guide-for-dynamic-maps"></a>StylesObject-séma – útmutató dinamikus térképekhez

> [!IMPORTANT]
> A Azure Maps Creator Services jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

 A a `StylesObject` `StyleObject` stateset-stílusokat jelképező tömb. Használja a Azure Maps Creator (előzetes verzió) [szolgáltatás állapot szolgáltatását](/rest/api/maps/featurestate) , hogy a stateset stílusait a beltéri Térkép adatszolgáltatásaira alkalmazza. Miután létrehozta a stateset stílusait, és hozzárendelte őket a beltéri térképi funkciókhoz, használhatja őket dinamikus beltéri térképek létrehozásához. A dinamikus beltéri térképek létrehozásával kapcsolatos további információkért lásd: [dinamikus stílus implementálása a Creator beltéri térképekhez](indoor-map-dynamic-styling.md).

## <a name="styleobject"></a>StyleObject

A a `StyleObject` következő stílusú szabályok egyike:

 * [`BooleanTypeStyleRule`](#booleantypestylerule)
 * [`NumericTypeStyleRule`](#numerictypestylerule)
 * [`StringTypeStyleRule`](#stringtypestylerule)

Az alábbi JSON-példa a három stílus típusának használatát mutatja be.  A a `BooleanTypeStyleRule` szolgáltatás a dinamikus stílus meghatározására szolgál, amelynek a `occupied` tulajdonsága igaz és hamis.  A azon `NumericTypeStyleRule` szolgáltatások stílusának meghatározására szolgál, amelyek `temperature` tulajdonsága egy adott tartományon belül esik. Végül a a `StringTypeStyleRule` megadott stílusoknak megfelelőre van felhasználva `meetingType` .



```json
 "styles": [
     {
        "keyname": "occupied",
        "type": "boolean",
        "rules": [
            {
                "true": "#FF0000",
                "false": "#00FF00"
            }
        ]
    },
    {
        "keyname": "temperature",
        "type": "number",
        "rules": [
             {
                "range": {
                "minimum": 50,
                "exclusiveMaximum": 70
                },
                "color": "#343deb"
            },
            {
                "range": {
                "maximum": 70,
                "exclusiveMinimum": 30
              },
              "color": "#eba834"
            }
        ]
    },
    {
      "keyname": "meetingType",
      "type": "string",
      "rules": [
        {
          "private": "#FF0000",
          "confidential": "#FF00AA",
          "allHands": "#00FF00",
          "brownBag": "#964B00"
        }
      ]
    }
]
```

## <a name="numerictypestylerule"></a>NumericTypeStyleRule

 A a `NumericTypeStyleRule`  [`StyleObject`](#styleobject) és a a következő tulajdonságokat tartalmazza:

| Tulajdonság | Típus | Leírás | Kötelező |
|-----------|----------|-------------|-------------|
| `keyName` | sztring | Az *állapot* vagy a dinamikus tulajdonság neve. Az értéknek `keyName` egyedinek kell lennie a `StyleObject` tömb belsejében.| Yes |
| `type` | sztring | Az érték "numerikus". | Yes |
| `rules` | [`NumberRuleObject`](#numberruleobject)[]| Numerikus stílusú tartományok tömbje, amelyekhez társított színek tartoznak. Az egyes tartományok olyan színeket határoznak meg, amelyeket akkor kell használni, ha az *állapot* megfelel a tartománynak.| Yes |

### <a name="numberruleobject"></a>NumberRuleObject

A egy `NumberRuleObject` [`RangeObject`](#rangeobject) és egy `color` tulajdonságot tartalmaz. Ha az *állapot* értéke a tartományba esik, a megjelenített szín a tulajdonságban megadott szín lesz `color` .

Ha több átfedésben lévő tartományt határoz meg, akkor a választott szín az első megtalált tartományban megadott szín lesz.

A következő JSON-mintában mindkét tartomány igaz értéket fog tartani, ha az *állapot* értéke 50-60. Azonban a használni kívánt szín a `#343deb` lista első olyan tartománya, amely teljesült.

```json

    {
        "rules":[
            {
                "range": {
                "minimum": 50,
                "exclusiveMaximum": 70
                },
                "color": "#343deb"
            },
            {
                "range": {
                "minimum": 50,
                "maximum": 60
                },
                "color": "#eba834"
            }
        ]
    }
]
```

| Tulajdonság | Típus | Leírás | Kötelező |
|-----------|----------|-------------|-------------|
| `range` | [RangeObject](#rangeobject) | A [RangeObject](#rangeobject) logikai tartományra vonatkozó feltételeket határoz meg, amelyek esetében az `true` *állapot* megjelenítési színét a tulajdonságban megadott színre kell módosítani `color` . Ha `range` nincs megadva, akkor a tulajdonságban definiált szín `color` mindig fel lesz használva.   | No |
| `color` | sztring | Az a szín, amelyet akkor kell használni, ha az állapot értéke a tartományba esik. A `color` tulajdonság egy JSON-karakterlánc a következő formátumok valamelyikében: <ul><li> HTML-stílusú hexadecimális értékek </li><li> RGB ("#ff0", "#ffff00", "RGB (255, 255, 0)")</li><li> RGBA ("RGBA (255, 255, 0, 1)")</li><li> HSL ("HSL (100, 50%, 50%)")</li><li> HSLA ("HSLA (100, 50%, 50%, 1)")</li><li> Előre definiált HTML-színek nevei, például sárga és kék.</li></ul> | Yes |

### <a name="rangeobject"></a>RangeObject

A `RangeObject` meghatározza az a numerikus tartomány értékét [`NumberRuleObject`](#numberruleobject) . Ahhoz, hogy az *állapot* értéke a tartományba essen, az összes megadott feltételnek igaznak kell lennie.

| Tulajdonság | Típus | Leírás | Kötelező |
|-----------|----------|-------------|-------------|
| `minimum` | double | Az x ≥ x `minimum` . szám.| No |
| `maximum` | double | Az x összes x ≤ betűje `maximum` . | No |
| `exclusiveMinimum` | double | Az x által > x szám `exclusiveMinimum` .| No |
| `exclusiveMaximum` | double | Az x által < x szám `exclusiveMaximum` .| No |

### <a name="example-of-numerictypestylerule"></a>Példa NumericTypeStyleRule

A következő JSON egy `NumericTypeStyleRule` nevű *állapotot* ábrázol `temperature` . Ebben a példában a [`NumberRuleObject`](#numberruleobject) két meghatározott hőmérsékleti tartományt és a hozzájuk társított színstílusokat tartalmazza. Ha a hőmérséklet tartománya 50-69, a kijelzőnek a színt kell használnia `#343deb` .  Ha a hőmérséklet tartománya 31-70, a kijelzőnek a színt kell használnia `#eba834` .

```json
{
    "keyname": "temperature",
    "type": "number",
    "rules":[
        {
            "range": {
            "minimum": 50,
            "exclusiveMaximum": 70
            },
            "color": "#343deb"
        },
        {
            "range": {
            "maximum": 70,
            "exclusiveMinimum": 30
            },
            "color": "#eba834"
        }
    ]
}
```

## <a name="stringtypestylerule"></a>StringTypeStyleRule

A a `StringTypeStyleRule` [`StyleObject`](#styleobject) és a a következő tulajdonságokat tartalmazza:

| Tulajdonság | Típus | Leírás | Kötelező |
|-----------|----------|-------------|-------------|
| `keyName` | sztring |  Az *állapot* vagy a dinamikus tulajdonság neve.  Az értéknek `keyName` egyedinek kell lennie a  `StyleObject` tömb belsejében.| Yes |
| `type` | sztring |Az érték a "string". | Yes |
| `rules` | [`StringRuleObject`](#stringruleobject)[]| N számú *állapotinformációkat* tartalmazó tömb.| Yes |

### <a name="stringruleobject"></a>StringRuleObject

A egy `StringRuleObject` akár N számú állapotinformációkat is tartalmaz, amelyek a funkció tulajdonságának lehetséges karakterlánc-értékei. Ha a szolgáltatás tulajdonságának értéke nem egyezik a definiált állapotok egyikével sem, akkor a funkció nem tartalmaz dinamikus stílust. Ha ismétlődő állapotinformációkat adnak meg, az első elsőbbséget élvez.

A karakterlánc-érték egyeztetése megkülönbözteti a kis-és nagybetűket.

| Tulajdonság | Típus | Leírás | Kötelező |
|-----------|----------|-------------|-------------|
| `stateValue1` | sztring | A szín, ha az érték karakterlánca stateValue1. | No |
| `stateValue2` | sztring | A szín, ha az érték karakterlánca stateValue. | No |
| `stateValueN` | sztring | A szín, ha az érték karakterlánca stateValueN. | No |

### <a name="example-of-stringtypestylerule"></a>Példa StringTypeStyleRule

A következő JSON `StringTypeStyleRule` azt szemlélteti, hogy az adott értekezlet-típusokhoz társított stílusok definiálva vannak.

```json
    {
      "keyname": "meetingType",
      "type": "string",
      "rules": [
        {
          "private": "#FF0000",
          "confidential": "#FF00AA",
          "allHands": "#00FF00",
          "brownBag": "#964B00"
        }
      ]
    }

```

## <a name="booleantypestylerule"></a>BooleanTypeStyleRule

A a `BooleanTypeStyleRule` [`StyleObject`](#styleobject) és a a következő tulajdonságokat tartalmazza:

| Tulajdonság | Típus | Leírás | Kötelező |
|-----------|----------|-------------|-------------|
| `keyName` | sztring |  Az *állapot* vagy a dinamikus tulajdonság neve.  Az értéknek `keyName` egyedinek kell lennie a `StyleObject`  tömb belsejében.| Yes |
| `type` | sztring |Az érték "boolean". | Yes |
| `rules` | [`BooleanRuleObject`](#booleanruleobject)1| Logikai pár, amely színekkel `true` és `false` *állapotokkal* rendelkezik.| Yes |

### <a name="booleanruleobject"></a>BooleanRuleObject

A `BooleanRuleObject` meghatározza a színeket `true` és az `false` értékeket.

| Tulajdonság | Típus | Leírás | Kötelező |
|-----------|----------|-------------|-------------|
| `true` | sztring | Az *állapot* értékének megadásakor használandó szín `true` A `color` tulajdonság egy JSON-karakterlánc a következő formátumok valamelyikében: <ul><li> HTML-stílusú hexadecimális értékek </li><li> RGB ("#ff0", "#ffff00", "RGB (255, 255, 0)")</li><li> RGBA ("RGBA (255, 255, 0, 1)")</li><li> HSL ("HSL (100, 50%, 50%)")</li><li> HSLA ("HSLA (100, 50%, 50%, 1)")</li><li> Előre definiált HTML-színek nevei, például sárga és kék.</li></ul>| Yes |
| `false` | sztring | Az *állapot* értékének megadásakor használandó szín `false` | Yes |

### <a name="example-of-booleantypestylerule"></a>Példa BooleanTypeStyleRule

A következő JSON egy `BooleanTypeStyleRule` nevű *állapotot* ábrázol `occupied` . A [`BooleanRuleObject`](#booleanruleobject) meghatározza a színeket `true` és az `false` értékeket.

```json
{
    "keyname": "occupied",
    "type": "boolean",
    "rules": [
    {
        "true": "#FF0000",
        "false": "#00FF00"
    }
    ]
}
```
---
title: Telemetria processzorok (előzetes verzió) – Azure Monitor Application Insights Javához
description: Ismerje meg, hogyan konfigurálhatja a telemetria-processzorokat Azure Monitor Application Insights Javához.
ms.topic: conceptual
ms.date: 10/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: 35e53454e5b2c6265082bbedb4a8b60e82df7191
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101734570"
---
# <a name="telemetry-processors-preview---azure-monitor-application-insights-for-java"></a>Telemetria processzorok (előzetes verzió) – Azure Monitor Application Insights Javához

> [!NOTE]
> A telemetria processzorok funkciója előzetes verzióban érhető el.

A Application Insights Java 3,0-ügynöke a telemetria-adatfeldolgozást az adatexportálás előtt képes feldolgozni.

Íme néhány felhasználási eset a telemetria processzorok esetében:
 * Bizalmas adatok létrehozása.
 * Adja meg az egyéni dimenziók feltételeit.
 * Frissítse a span nevet, amely a Azure Portal hasonló telemetria összesítésére szolgál.
 * Drop span attribútumok a betöltési költségek szabályozásához.

## <a name="terminology"></a>Terminológia

Mielőtt elsajátítja a telemetria processzorokat, ismernie kell *az időtartamot.* A span a következő általános kifejezése:

* Egy bejövő kérelem.
* Kimenő függőség (például távoli hívás egy másik szolgáltatásnak).
* Folyamaton belüli függőség (például a szolgáltatás alösszetevői végzik a munkát).

A telemetria processzorok esetében ezek a span-összetevők fontosak:

* Name
* Attribútumok

A span neve a kérések és a függőségek elsődleges megjelenítése a Azure Portalban. A span attribútumok az adott kérelem vagy függőség standard és egyéni tulajdonságait jelölik.

## <a name="telemetry-processor-types"></a>Telemetria processzor típusai

Jelenleg a telemetria processzorok kétféle típusa van az attribútumok processzorai és a span processzorok számára.

Az attribútum-processzor beillesztheti, frissítheti, törölheti vagy kivonatoló attribútumokat adhat meg.
Használhat egy reguláris kifejezést is egy vagy több új attribútum egy meglévő attribútumból való kinyeréséhez.

A span processzorok frissíthetik a telemetria nevét.
Egy reguláris kifejezés használatával is kinyerhető egy vagy több új attribútum a span nevéből.

> [!NOTE]
> A telemetria-processzorok jelenleg csak a String típusú attribútumokat dolgozzák fel. A logikai vagy a szám típusú attribútumokat nem dolgozzák fel.

## <a name="getting-started"></a>Első lépések

A kezdéshez hozzon létre egy *applicationinsights.js* nevű konfigurációs fájlt. Mentse azt ugyanabban a könyvtárban, mint a *applicationinsights-Agent- \* . jar*. Használja az alábbi sablont.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        ...
      },
      {
        "type": "attribute",
        ...
      },
      {
        "type": "span",
        ...
      }
    ]
  }
}
```

## <a name="include-criteria-and-exclude-criteria"></a>Feltételek és kizáró feltételek belefoglalása

Mindkét attribútum-feldolgozó és a span processzor támogatja `include` a választható és a `exclude` feltételt.
A processzort csak azokra a feltétekre lehet alkalmazni, amelyek megfelelnek a feltételeknek (ha meg van határozva `include` ) _, és_ nem felelnek `exclude` meg a feltételeinek (ha meg van határozva).

Ha ezt a beállítást szeretné konfigurálni, a `include` vagy a `exclude` (vagy mindkettő) területen adja meg a legalább egyet, illetve a vagy a elemet `matchType` `spanNames` `attributes` .
A belefoglalási konfiguráció több megadott feltételt is engedélyez.
Az összes megadott feltételnek igaz értékre kell kiértékelnie, hogy egyezést eredményez. 

* **Kötelező mező**: `matchType` szabályozza a `spanNames` tömbökben és `attributes` tömbökben lévő elemek értelmezését. A lehetséges értékek a következők: `regexp` és `strict` . 

* Nem **kötelező mezők**: 
    * `spanNames` legalább egy elemnek meg kell egyeznie. 
    * `attributes` meghatározza az egyeztetni kívánt attribútumok listáját. Ezeknek az attribútumoknak pontosan egyezniük kell, hogy egyezzenek a találatokkal.
    
> [!NOTE]
> Ha mindkettő `include` és `exclude` a meg van adva, a tulajdonságokat a `include` Tulajdonságok ellenőrzése előtt ellenőrzi a rendszer `exclude` .

### <a name="sample-usage"></a>Minta használata

```json
"processors": [
  {
    "type": "attribute",
    "include": {
      "matchType": "strict",
      "spanNames": [
        "spanA",
        "spanB"
      ]
    },
    "exclude": {
      "matchType": "strict",
      "attributes": [
        {
          "key": "redact_trace",
          "value": "false"
        }
      ]
    },
    "actions": [
      {
        "key": "credit_card",
        "action": "delete"
      },
      {
        "key": "duplicate_key",
        "action": "delete"
      }
    ]
  }
]
```
További információ: [telemetria Processor examples](./java-standalone-telemetry-processors-examples.md).

## <a name="attribute-processor"></a>Attribútum processzora

Az attribútum-feldolgozó módosítja egy span attribútumait. Támogathatja az elterjedések belefoglalásának vagy kizárásának lehetőségét is. A konfigurációs fájl által megadott sorrendben végrehajtott műveletek listáját veszi figyelembe. A processzor a következő műveleteket támogatja:

- `insert`
- `update`
- `delete`
- `hash`
- `extract`
### `insert`

A `insert` művelet beszúr egy új attribútumot a megjelenő ablakba, ahol a kulcs még nem létezik.   

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "value": "value1",
        "action": "insert"
      }
    ]
  }
]
```
A `insert` művelethez a következő beállítások szükségesek:
* `key`
* `value`Vagy`fromAttribute`
* `action`: `insert`

### `update`

A `update` művelet frissíti egy attribútumot az átfedésekben, ahol a kulcs már létezik.

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "value": "newValue",
        "action": "update"
      }
    ]
  }
]
```
A `update` művelethez a következő beállítások szükségesek:
* `key`
* `value`Vagy`fromAttribute`
* `action`: `update`


### `delete` 

A `delete` művelet töröl egy attribútumot egy tartományból.

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "action": "delete"
      }
    ]
  }
]
```
A `delete` művelethez a következő beállítások szükségesek:
* `key`
* `action`: `delete`

### `hash`

A `hash` művelet kivonatai (SHA1) egy meglévő attribútumérték.

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "action": "hash"
      }
    ]
  }
]
```
A `hash` művelethez a következő beállítások szükségesek:
* `key`
* `action`: `hash`

### `extract`

> [!NOTE]
> A `extract` szolgáltatás csak a 3.0.2 és újabb verziókban érhető el.

A `extract` művelet kibontja az értékeket egy reguláris kifejezéssel a bemeneti kulcsból a szabály által megadott kulcsokra. Ha a célként megadott kulcs már létezik, felülbírálva van. Ez a művelet úgy viselkedik, mint a [kiterjedési processzor](#extract-attributes-from-the-span-name) `toAttributes` beállítása, ahol a meglévő attribútum a forrás.

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "pattern": "<regular pattern with named matchers>",
        "action": "extract"
      }
    ]
  }
]
```
A `extract` művelethez a következő beállítások szükségesek:
* `key`
* `pattern`
* `action`: `extract`

További információ: [telemetria Processor examples](./java-standalone-telemetry-processors-examples.md).

## <a name="span-processor"></a>Span processzor

A span processzor a span neve alapján módosítja a span tartomány nevét vagy attribútumait. Támogathatja az elterjedések belefoglalásának vagy kizárásának lehetőségét is.

### <a name="name-a-span"></a>Egy span neve

A `name` szakaszhoz a `fromAttributes` beállítás szükséges. Ezeknek az attribútumoknak az értékei a konfiguráció által megadott sorrendben összefűzött új név létrehozására szolgálnak. A processzor csak akkor fogja módosítani a span Name nevet, ha az összes ilyen attribútum megtalálható a teljes tartományon.

A `separator` beállítás megadása nem kötelező. Ez a beállítás egy karakterlánc. Az értékek felosztására van beállítva.
> [!NOTE]
> Ha az Átnevezés az attribútumok processzorra támaszkodik az attribútumok módosításához, győződjön meg arról, hogy a folyamat-specifikációban az attribútumok processzora van megadva a span processzor.

```json
"processors": [
  {
    "type": "span",
    "name": {
      "fromAttributes": [
        "attributeKey1",
        "attributeKey2",
      ],
      "separator": "::"
    }
  }
] 
```

### <a name="extract-attributes-from-the-span-name"></a>Attribútumok kinyerése a span nevéből

A `toAttributes` szakasz felsorolja azokat a reguláris kifejezéseket, amelyek megfelelnek a span nevének. Attribútumok kibontása alkifejezések alapján.

A `rules` beállítás megadása kötelező. Ez a beállítás felsorolja azokat a szabályokat, amelyek segítségével kinyerheti az attribútumok értékeit a tartomány nevéből. 

A span Name értékeit a kinyert attribútumok neve váltja fel. A lista minden szabálya egy reguláris kifejezés (regex) mintázatának karakterlánca. 

Az értékek a kinyert attribútumok neveivel helyettesíthetők:

1. A span nevet a rendszer a regexben ellenőrzi. 
1. Ha a regex egyezik, a regex összes megnevezett alkifejezése attribútumként lesz kibontva. 
1. A kibontott attribútumok hozzáadódnak a tartományhoz. 
1. Minden alkifejezés neve egy attribútum neve lesz. 
1. Az alkifejezés egyezésének értéke lesz az attribútumérték. 
1. Az span Name egyező részét a kinyert attribútum neve váltja fel. Ha az attribútumok már léteznek a span-ban, a rendszer felülírja őket. 
 
Ezt a folyamatot a megadott sorrendben minden szabálynál meg kell ismételni. Minden további szabály az előző szabály kimenetét képező span névben működik.

```json
"processors": [
  {
    "type": "span",
    "name": {
      "toAttributes": {
        "rules": [
          "rule1",
          "rule2",
          "rule3"
        ]
      }
    }
  }
]

```

## <a name="common-span-attributes"></a>Általános span attribútumok

Ez a szakasz felsorolja a telemetria processzorok által használható általános span-attribútumokat.

### <a name="http-spans"></a>HTTP-Átívelés

| Attribútum  | Típus | Description | 
|---|---|---|
| `http.method` | sztring | HTTP-kérelem módszere.|
| `http.url` | sztring | Teljes HTTP-kérelem URL-címe az űrlapon `scheme://host[:port]/path?query[#fragment]` . A töredék általában nem HTTP-n keresztül történik. Ha azonban ismert a töredék, azt bele kell foglalni.|
| `http.status_code` | szám | [Http-válasz állapotának kódja](https://tools.ietf.org/html/rfc7231#section-6)|
| `http.flavor` | sztring | A HTTP-protokoll típusa. |
| `http.user_agent` | sztring | Az ügyfél által eljuttatott [http User-Agent](https://tools.ietf.org/html/rfc7231#section-5.5.3) fejléc értéke. |

### <a name="jdbc-spans"></a>JDBC-átívelő

| Attribútum  | Típus | Description  |
|---|---|---|
| `db.system` | sztring | Az adatbázis-kezelő rendszer (adatbázisok) által használt termék azonosítója. |
| `db.connection_string` | sztring | Az adatbázishoz való kapcsolódáshoz használt kapcsolati karakterlánc. Javasoljuk, hogy távolítsa el a beágyazott hitelesítő adatokat.|
| `db.user` | sztring | Az adatbázis eléréséhez használt Felhasználónév. |
| `db.name` | sztring | Az elérni kívánt adatbázis nevének jelentésére szolgáló sztring. Az adatbázist átváltó parancsok esetén a karakterláncot a célként megadott adatbázisra kell beállítani, még akkor is, ha a parancs végrehajtása sikertelen.|
| `db.statement` | sztring | Az adatbázis-utasítás fut.|

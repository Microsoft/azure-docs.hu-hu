---
title: Szerzői szabályzatok a tömb tulajdonságaihoz az erőforrásokon
description: Megismerheti a tömb paramétereinek és a tömb nyelvi kifejezéseknek a használatát, kiértékelheti a [*] aliast, és hozzáfűzheti az elemeket Azure Policy definíciós szabályokkal.
ms.date: 10/22/2020
ms.topic: how-to
ms.openlocfilehash: 650b2ec6bc1bbd12cd10abb1917ef5ea2d6029e9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98220745"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Az Azure-erőforrások tömb tulajdonságainak szerzői szabályzatai

A Azure Resource Manager tulajdonságok általában karakterláncként és logikai értékként vannak definiálva. Ha egy-a-többhöz kapcsolat létezik, az összetett tulajdonságok tömbként vannak definiálva. Azure Policy a tömbök számos különböző módon használatosak:

- Egy [definíciós paraméter](../concepts/definition-structure.md#parameters)típusa több beállítás megadásához
- Egy házirend- [szabály](../concepts/definition-structure.md#policy-rule) része a vagy a  **notIn** feltételek használatával
- Egy olyan házirend-szabály része, amely megszámolja, hogy hány tömb tagja felel meg a feltételnek
- Meglévő tömb frissítéséhez a [Hozzáfűzés](../concepts/effects.md#append) és a [módosítás](../concepts/effects.md#modify) effektusban

Ez a cikk a Azure Policy egyes használatát ismerteti, és számos példát tartalmaz.

## <a name="parameter-arrays"></a>Paraméterek tömbök

### <a name="define-a-parameter-array"></a>Paraméter-tömb definiálása

A paraméter tömbként való meghatározása lehetővé teszi a szabályzat rugalmasságát, ha egynél több értékre van szükség.
Ez a szabályzat-definíció lehetővé teszi, hogy a **allowedLocations** paraméter egyetlen helye legyen, és az alapértelmezett érték a _eastus2_:

```json
"parameters": {
    "allowedLocations": {
        "type": "string",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2"
    }
}
```

A **Type** _karakterlánc_ volt, csak egy érték állítható be a szabályzat kiosztásakor. Ha ez a szabályzat hozzá van rendelve, a hatókörben lévő erőforrások csak egyetlen Azure-régión belül engedélyezettek. A legtöbb szabályzat-definíciónak lehetővé kell tennie a jóváhagyott beállítások listáját, például a _eastus2_, a _eastus_ és a _westus2_ engedélyezését.

Ha a házirend-definíciót több beállítás engedélyezéséhez szeretné létrehozni, használja a _tömb_ **típusát**. Ugyanezt a szabályzatot a következőképpen lehet újraírni:

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2",
        "allowedValues": [
            "eastus2",
            "eastus",
            "westus2"
        ]

    }
}
```

> [!NOTE]
> A házirend-definíció mentése után a paraméter **Type** tulajdonsága nem módosítható.

Ez az új paraméter-definíció egynél több értéket vesz igénybe a szabályzat-hozzárendelés során. Ha a tömb tulajdonsága **allowedValues** van definiálva, a hozzárendelés során elérhető értékek tovább korlátozódnak az előre definiált lehetőségek listájára. A **allowedValues** használata nem kötelező.

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>Értékek átadása egy paraméter-tömbnek a hozzárendelés során

Ha a házirendet a Azure Portalon keresztül rendeli hozzá, a _tömb_ **típusú** paraméterek egyetlen szövegmezőként jelennek meg. A tipp a "use; az értékek elkülönítéséhez. (pl.: London; New York) ". Ha át szeretné adni a _eastus2_, a _eastus_ és a _westus2_ engedélyezett tárolási értékeit a paraméternek, használja a következő karakterláncot:

`eastus2;eastus;westus2`

A paraméter értékének formátuma eltérő az Azure CLI, Azure PowerShell vagy a REST API használatakor. Az értékeket egy JSON-karakterlánc adja át, amely tartalmazza a paraméter nevét is.

```json
{
    "allowedLocations": {
        "value": [
            "eastus2",
            "eastus",
            "westus2"
        ]
    }
}
```

Ha ezt a sztringet az egyes SDK-kal szeretné használni, használja a következő parancsokat:

- Azure CLI: parancs [az Policy hozzárendelés-létrehozás](/cli/azure/policy/assignment#az_policy_assignment_create) paraméter **-paraméterekkel**
- Azure PowerShell: parancsmag [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) paraméterrel **PolicyParameter**
- REST API: a _put_ [create](/rest/api/resources/policyassignments/create) művelet a kérelem törzsének részeként a **Tulajdonságok. Parameters** tulajdonság értékeként

## <a name="using-arrays-in-conditions"></a>Tömbök használata a feltételekben

### <a name="in-and-notin"></a>`In` és `notIn`

A `in` és a `notIn` feltételek csak a tömb értékeit vehetik működésbe. Egy tömbben lévő érték meglétének ellenőrzéséhez. A tömb lehet literális JSON-tömb vagy egy Array paraméterre mutató hivatkozás. Például:

```json
{
      "field": "tags.environment",
      "in": [ "dev", "test" ]
}
```

```json
{
      "field": "location",
      "notIn": "[parameters('allowedLocations')]"
}
```

### <a name="value-count"></a>Értékek száma

A [Value Count](../concepts/definition-structure.md#value-count) kifejezés azt adja meg, hogy hány tömb tagja felel meg a feltételnek. Lehetővé teszi, hogy ugyanazt a feltételt többször is kiértékelje, az egyes iterációk különböző értékeit használva. A következő feltétel például ellenőrzi, hogy az erőforrás neve megegyezik-e a minták tömbje által használt mintázattal:

```json
{
    "count": {
        "value": [ "test*", "dev*", "prod*" ],
        "name": "pattern",
        "where": {
            "field": "name",
            "like": "[current('pattern')]"
        }
    },
    "greater": 0
}
```

A kifejezés kiértékeléséhez Azure Policy kiértékeli a `where` feltételt az egyes tagok esetében 3 alkalommal, egyszer, hogy hányszor értékelte ki a rendszer `[ "test*", "dev*", "prod*" ]` `true` . Minden iteráció esetén az aktuális tömb tag értéke párosítva lesz a `pattern` által definiált index nevével `count.name` . Ezt az értéket ezután a feltételen belül lehet hivatkozni `where` egy speciális sablon függvény meghívásával: `current('pattern')` .

| Iteráció | `current('pattern')` visszaadott érték |
|:---|:---|
| 1 | `"test*"` |
| 2 | `"dev*"` |
| 3 | `"prod*"` |

A feltétel csak akkor igaz, ha az eredményül kapott darabszám 0-nál nagyobb.

Ha a feltételt általánosabban szeretné tenni, használja a paraméter hivatkozását literális tömb helyett:

 ```json
{
    "count": {
        "value": "[parameters('patterns')]",
        "name": "pattern",
        "where": {
            "field": "name",
            "like": "[current('pattern')]"
        }
    },
    "greater": 0
}
```

Ha a **Value Count** kifejezés nem tartozik más **Count** kifejezés alá, nem `count.name` kötelező, és a `current()` függvény argumentumok nélkül is használható:

```json
{
    "count": {
        "value": "[parameters('patterns')]",
        "where": {
            "field": "name",
            "like": "[current()]"
        }
    },
    "greater": 0
}
```

Az **értékek száma** a komplex objektumok tömbjét is támogatja, ami összetettebb feltételeket tesz lehetővé. Például a következő feltétel határozza meg a kívánt címke értékét az egyes nevekhez, és ellenőrzi, hogy az erőforrás neve megegyezik-e a mintázattal, de nem rendelkezik a szükséges címke értékkel:

```json
{
    "count": {
        "value": [
            { "pattern": "test*", "envTag": "dev" },
            { "pattern": "dev*", "envTag": "dev" },
            { "pattern": "prod*", "envTag": "prod" },
        ],
        "name": "namePatternRequiredTag",
        "where": {
            "allOf": [
                {
                    "field": "name",
                    "like": "[current('namePatternRequiredTag').pattern]"
                },
                {
                    "field": "tags.env",
                    "notEquals": "[current('namePatternRequiredTag').envTag]"
                }
            ]
        }
    },
    "greater": 0
}
```

Hasznos példákat itt talál: [Value Count példák](../concepts/definition-structure.md#value-count-examples).

## <a name="referencing-array-resource-properties"></a>Hivatkozás a tömb erőforrásának tulajdonságaira

Számos felhasználási eset esetében szükséges a tömb tulajdonságainak használata a kiértékelt erőforrásban. Egyes esetekben a teljes tömbre kell hivatkozni (például a hosszának ellenőrzése). Másoknak minden egyes tömbhöz meg kell tenniük egy feltételt (például gondoskodni kell arról, hogy minden tűzfalszabály blokkolja az internetről való hozzáférést). A különböző módok megismerése Azure Policy hivatkozhat az erőforrás-tulajdonságokra, valamint arról, hogy ezek a hivatkozások hogyan viselkedjenek, amikor a tömb tulajdonságaira hivatkoznak.

### <a name="referencing-resource-properties"></a>Viszonyítási erőforrás tulajdonságai

Az erőforrás-tulajdonságokat Azure Policy hivatkozhat az [aliasok](../concepts/definition-structure.md#aliases) használatával két módon hivatkozhat egy erőforrás-tulajdonság értékeire Azure Policyn belül:

- A [mező](../concepts/definition-structure.md#fields) feltételének használatával ellenőrizhető, hogy **az összes** kiválasztott erőforrás-tulajdonság megfelel-e egy feltételnek. Példa:

  ```json
  {
    "field" : "Microsoft.Test/resourceType/property",
    "equals": "value"
  }
  ```

- A `field()` függvény használata egy tulajdonság értékének eléréséhez. Példa:

  ```json
  {
    "value": "[take(field('Microsoft.Test/resourceType/property'), 7)]",
    "equals": "prefix_"
  }
  ```

A mező feltétele implicit "all of" viselkedést tartalmaz. Ha az alias az értékek gyűjteményét jelöli, ellenőrzi, hogy minden egyes érték megfelel-e a feltételnek. A `field()` függvény visszaadja az alias által megadott értékeket a-ként, amelyet aztán más sablon-függvények is kezelhetnek.

### <a name="referencing-array-fields"></a>Tömb mezőinek hivatkozása

A tömb erőforrás-tulajdonságait általában két különböző típusú alias jelképezi. Egy "normál" alias és egy [tömb aliasneve](../concepts/definition-structure.md#understanding-the--alias) , amelyhez `[*]` csatolva van:

- `Microsoft.Test/resourceType/stringArray`
- `Microsoft.Test/resourceType/stringArray[*]`

#### <a name="referencing-the-array"></a>A tömbre hivatkozik

Az első alias egyetlen értéket képvisel, a `stringArray` kérelem tartalmából származó tulajdonság értékét. Mivel a tulajdonság értéke tömb, nem nagyon hasznos a házirend feltételeiben. Például:

```json
{
  "field": "Microsoft.Test/resourceType/stringArray",
  "equals": "..."
}
```

Ez az állapot összehasonlítja a teljes `stringArray` tömböt egyetlen karakterlánc-értékkel. A legtöbb feltétel, beleértve `equals` a, csak a karakterlánc-értékeket fogadja el, így nem sokat használható a tömb összevetése egy karakterlánccal. A Array tulajdonságra hivatkozó fő forgatókönyv akkor hasznos, ha ellenőrzi, hogy létezik-e:

```json
{
  "field": "Microsoft.Test/resourceType/stringArray",
  "exists": "true"
}
```

A `field()` függvény használatával a visszaadott érték a kérelem tartalmából származó tömb, amely a tömb argumentumait fogadó [támogatott sablon-függvények](../concepts/definition-structure.md#policy-functions) bármelyikével használható. A következő feltétel például ellenőrzi, hogy a hossza nagyobb-e `stringArray` 0-nál:

```json
{
  "value": "[length(field('Microsoft.Test/resourceType/stringArray'))]",
  "greater": 0
}
```

#### <a name="referencing-the-array-members-collection"></a>A tömb tagjainak gyűjteményére való hivatkozás

A szintaxist használó aliasok `[*]` egy **Array tulajdonságból kiválasztott tulajdonságértékek gyűjteményét** jelölik, ami eltér a tömb tulajdonságának kiválasztásával. A esetében `Microsoft.Test/resourceType/stringArray[*]` egy olyan gyűjteményt ad vissza, amely az összes tagjával rendelkezik `stringArray` . Ahogy azt korábban említettük, egy `field` feltétel ellenőrzi, hogy az összes kiválasztott erőforrás-tulajdonság megfelel-e a feltételnek, ezért a következő feltétel csak akkor igaz, ha az **összes** tagja "" `stringArray` értékkel egyenlő.

```json
{
  "field": "Microsoft.Test/resourceType/stringArray[*]",
  "equals": "value"
}
```

Ha a tömb objektumokat tartalmaz, egy `[*]` alias használható egy adott tulajdonság értékének kiválasztásához az egyes tömb tagokból. Példa:

```json
{
  "field": "Microsoft.Test/resourceType/objectArray[*].property",
  "equals": "value"
}
```

Ez az állapot akkor igaz, ha az összes `property` tulajdonság értéke `objectArray` egyenlő `"value"` . További példákat itt talál: [további \[ \* \] alias-példák](#appendix--additional--alias-examples).

Ha a `field()` függvényt egy tömb aliasára hivatkozik, a visszaadott érték az összes kijelölt érték tömbje. Ez azt jelenti, hogy a függvény általános használati esete, hogy a `field()` sablon függvények az erőforrás-tulajdonságok értékekre való alkalmazása igen korlátozott. Ebben az esetben csak az olyan sablon-függvények használhatók, amelyek elfogadják a tömb argumentumait. Például lehetséges a tömb hosszának beolvasása a következővel: `[length(field('Microsoft.Test/resourceType/objectArray[*].property'))]` . Azonban összetettebb forgatókönyvek, például a Template függvény alkalmazása minden egyes tömb tagjaira, és a kívánt értékkel való összehasonlítás csak a kifejezés használata esetén lehetséges `count` . További információ: [Field Count kifejezés](#field-count-expressions).

Az összegzéshez tekintse meg a következő példában szereplő erőforrás-tartalmakat, valamint a különböző aliasok által visszaadott kiválasztott értékeket:

```json
{
  "tags": {
    "env": "prod"
  },
  "properties":
  {
    "stringArray": [ "a", "b", "c" ],
    "objectArray": [
      {
        "property": "value1",
        "nestedArray": [ 1, 2 ]
      },
      {
        "property": "value2",
        "nestedArray": [ 3, 4 ]
      }
    ]
  }
}
```

A példában szereplő erőforrás-tartalomhoz tartozó mező feltételének használatakor az eredmények a következők:

| Alias | Kijelölt értékek |
|:--- |:---|
| `Microsoft.Test/resourceType/missingArray` | `null` |
| `Microsoft.Test/resourceType/missingArray[*]` | Az értékek üres gyűjteménye. |
| `Microsoft.Test/resourceType/missingArray[*].property` | Az értékek üres gyűjteménye. |
| `Microsoft.Test/resourceType/stringArray` | `["a", "b", "c"]` |
| `Microsoft.Test/resourceType/stringArray[*]` | `"a"`, `"b"`, `"c"` |
| `Microsoft.Test/resourceType/objectArray[*]` |  `{ "property": "value1", "nestedArray": [ 1, 2 ] }`,<br/>`{ "property": "value2", "nestedArray": [ 3, 4 ] }`|
| `Microsoft.Test/resourceType/objectArray[*].property` | `"value1"`, `"value2"` |
| `Microsoft.Test/resourceType/objectArray[*].nestedArray` | `[ 1, 2 ]`, `[ 3, 4 ]` |
| `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` | `1`, `2`, `3`, `4` |

Ha a `field()` függvényt használja a példában szereplő erőforrás-tartalomhoz, az eredmények a következők:

| Expression | Visszaadott érték |
|:--- |:---|
| `[field('Microsoft.Test/resourceType/missingArray')]` | `""` |
| `[field('Microsoft.Test/resourceType/missingArray[*]')]` | `[]` |
| `[field('Microsoft.Test/resourceType/missingArray[*].property')]` | `[]` |
| `[field('Microsoft.Test/resourceType/stringArray')]` | `["a", "b", "c"]` |
| `[field('Microsoft.Test/resourceType/stringArray[*]')]` | `["a", "b", "c"]` |
| `[field('Microsoft.Test/resourceType/objectArray[*]')]` |  `[{ "property": "value1", "nestedArray": [ 1, 2 ] }, { "property": "value2", "nestedArray": [ 3, 4 ] }]`|
| `[field('Microsoft.Test/resourceType/objectArray[*].property')]` | `["value1", "value2"]` |
| `[field('Microsoft.Test/resourceType/objectArray[*].nestedArray')]` | `[[ 1, 2 ], [ 3, 4 ]]` |
| `[field('Microsoft.Test/resourceType/objectArray[*].nestedArray[*]')]` | `[1, 2, 3, 4]` |

### <a name="field-count-expressions"></a>Mezők darabszámának kifejezései

A [mezők száma](../concepts/definition-structure.md#field-count) kifejezésekkel megtudhatja, hány tömb tagjai felelnek meg egy feltételnek, és összehasonlítja a számot a célérték értékével. `Count` intuitívabb és sokoldalú a tömbök kiértékeléséhez a `field` feltételekhez képest. A szintaxis a következő:

```json
{
  "count": {
    "field": <[*] alias>,
    "where": <optional policy condition expression>
  },
  "equals|greater|less|any other operator": <target value>
}
```

Ha feltétel nélkül használja `where` , `count` egyszerűen egy tömb hosszát adja vissza. Az előző szakaszban található példa erőforrás-tartalommal a következő `count` kifejezés lesz kiértékelve, `true` mivel `stringArray` három taggal rendelkezik:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]"
  },
  "equals": 3
}
```

Ez a viselkedés beágyazott tömbökkel is működik. Például a következő `count` kifejezés kiértékelése megtörténik, `true` mivel a tömbökben négy tömb tag található `nestedArray` :

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]"
  },
  "greaterOrEquals": 4
}
```

A ereje a `count` `where` feltételben van. Ha meg van adva, Azure Policy enumerálja a tömb tagjait, és kiértékeli a feltételt, és megszámolja, hogy hány tömb tagja értékeli ki a rendszer `true` . Pontosabban, a `where` feltétel kiértékelésének minden iterációjában Azure Policy kiválaszt egy tömbbeli tagot ***i** _-t, és kiértékeli az erőforrás tartalmát a (z `where` ) * állapottal szemben, _Ha ***_ az a array_ * tagja. Az egyes iterációkban csak egy tömbös tag érhető el, így összetett feltételeket alkalmazhat az egyes tömb tagjain.

Példa:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "a"
    }
  },
  "equals": 1
}
```
A kifejezés kiértékeléséhez Azure Policy kiértékeli `count` a `where` feltételt az egyes tagok esetében 3 alkalommal, egyszer, hogy hányszor értékelte ki a rendszer `stringArray` `true` . Ha a `where` feltétel a `Microsoft.Test/resourceType/stringArray[*]` tömb tagjaira vonatkozik, ahelyett, hogy kijelöli a összes tagját `stringArray` , minden alkalommal csak egyetlen tömbös tagot fog kijelölni:

| Iteráció | Kijelölt `Microsoft.Test/resourceType/stringArray[*]` értékek | `where` Kiértékelés eredménye |
|:---|:---|:---|
| 1 | `"a"` | `true` |
| 2 | `"b"` | `false` |
| 3 | `"c"` | `false` |

Így a `count` vissza fog térni `1` .

Egy összetettebb kifejezés:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "allOf": [
        {
          "field": "Microsoft.Test/resourceType/objectArray[*].property",
          "equals": "value2"
        },
        {
          "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
          "greater": 2
        }
      ]
    }
  },
  "equals": 1
}
```

| Iteráció | Kijelölt értékek | `where` Kiértékelés eredménye |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value1"` </br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | `false` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value2"` </br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4`| `true` |

És így a `count` visszaadja `1` .

Az a tény, hogy a `where` kifejezés a **teljes** kérelem tartalmára van kiértékelve (csak a jelenleg enumerált tömb tag módosításaival), azt jelenti, hogy a `where` feltétel a tömbön kívüli mezőkre is vonatkozik:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "field": "tags.env",
      "equals": "prod"
    }
  }
}
```

| Iteráció | Kijelölt értékek | `where` Kiértékelés eredménye |
|:---|:---|:---|
| 1 | `tags.env` => `"prod"` | `true` |
| 2 | `tags.env` => `"prod"` | `true` |

A beágyazott Count kifejezések is engedélyezettek:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "allOf": [
        {
          "field": "Microsoft.Test/resourceType/objectArray[*].property",
          "equals": "value2"
        },
        {
          "count": {
            "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
            "where": {
              "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
              "equals": 3
            },
            "greater": 0
          }
        }
      ]
    }
  }
}
```
 
| Külső hurok iterációja | Kijelölt értékek | Belső hurok iterációja | Kijelölt értékek |
|:---|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value1`</br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | 1 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1` |
| 1 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value1`</br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | 2 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `2` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value2`</br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4` | 1 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value2`</br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4` | 2 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `4` |

#### <a name="accessing-current-array-member-with-template-functions"></a>A jelenlegi tömb tag elérése a Template functions szolgáltatással

A sablon funkcióinak használatakor a `current()` függvény használatával érheti el az aktuális tömb tag vagy a hozzá tartozó tulajdonságok értékeit. Az aktuális tömbhöz tartozó tag értékének eléréséhez adja át a `count.field` függvényben argumentumként megadott aliast a vagy annak bármelyik alárendelt aliasában `current()` . Például:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
        "value": "[current('Microsoft.Test/resourceType/objectArray[*].property')]",
        "like": "value*"
    }
  },
  "equals": 2
}

```

| Iteráció | `current()` visszaadott érték | `where` Kiértékelés eredménye |
|:---|:---|:---|
| 1 | Az `property` első tagjának értéke `objectArray[*]` : `value1` | `true` |
| 2 | Az `property` első tagjának értéke `objectArray[*]` : `value2` | `true` |

#### <a name="the-field-function-inside-where-conditions"></a>A mező függvény a feltételek között

A `field()` függvény használható az aktuális tömbbeli tag értékének elérésére is, ha a **Count** kifejezés nem egy **létezési feltételen** belül van (a `field()` függvény mindig az adott feltétel alapján értékelt erőforrást tekinti át). 
A `field()` kiértékelt tömbre hivatkozó viselkedés a következő fogalmakon alapul:
1. A Array aliasok az összes tömb tagjai közül kiválasztott értékek gyűjteményében vannak feloldva.
1. `field()` a tömb Aliasokra hivatkozó függvények a kijelölt értékekkel rendelkező tömböt adnak vissza.
1. Ha a feltételben a megszámolt tömb aliasára hivatkozik, a függvény egy olyan `where` gyűjteményt ad vissza, amely egyetlen, az aktuális iterációban kiértékelt értékkel rendelkező tömbből van kiválasztva.

Ez azt jelenti, hogy amikor a feltételben lévő függvénnyel hivatkozik a megszámolt tömbre, a függvény egy olyan `field()` `where` **tömböt ad vissza, amely egyetlen taggal** rendelkezik. Habár ez nem lehet intuitív, összhangban van azzal a gondolattal, hogy a Array aliasok mindig a kiválasztott tulajdonságok gyűjteményét adják vissza. Bemutatunk egy példát:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "[field('Microsoft.Test/resourceType/stringArray[*]')]"
    }
  },
  "equals": 0
}
```

| Iteráció | Kifejezés értékei | `where` Kiértékelés eredménye |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/stringArray[*]` => `"a"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "a" ]` | `false` |
| 2 | `Microsoft.Test/resourceType/stringArray[*]` => `"b"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "b" ]` | `false` |
| 3 | `Microsoft.Test/resourceType/stringArray[*]` => `"c"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "c" ]` | `false` |

Ezért ha egy függvénnyel el kell érnie a megszámolt tömb aliasának értékét `field()` , az azt jelenti, hogy a sablont egy sablon függvénysel kell becsomagolni `first()` :

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "[first(field('Microsoft.Test/resourceType/stringArray[*]'))]"
    }
  }
}
```

| Iteráció | Kifejezés értékei | `where` Kiértékelés eredménye |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/stringArray[*]` => `"a"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"a"` | `true` |
| 2 | `Microsoft.Test/resourceType/stringArray[*]` => `"b"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"b"` | `true` |
| 3 | `Microsoft.Test/resourceType/stringArray[*]` => `"c"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"c"` | `true` |

Hasznos példákért tekintse meg a [mezők száma példákat](../concepts/definition-structure.md#field-count-examples).

## <a name="modifying-arrays"></a>Tömbök módosítása

Az erőforrás [hozzáfűzési](../concepts/effects.md#append) és [módosítási](../concepts/effects.md#modify) tulajdonsága a létrehozás vagy a frissítés során. A tömb tulajdonságainak használatakor a hatások viselkedése attól függ, hogy a művelet megkísérli-e módosítani az  **\[\*\]** aliast, vagy sem:

> [!NOTE]
> Az `modify` aliasokkal való hatás jelenleg **előzetes** verzióban érhető el.

|Alias |Hatás | Eredmény |
|-|-|-|
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `append` | Ha hiányzik, a Azure Policy hozzáfűzi a hatás részleteiben megadott teljes tömböt. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `modify``add`művelettel | Ha hiányzik, a Azure Policy hozzáfűzi a hatás részleteiben megadott teljes tömböt. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `modify``addOrReplace`művelettel | Azure Policy hozzáfűzi az effektus részleteiben megadott teljes tömböt, ha hiányzik vagy lecseréli a meglévő tömböt. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `append` | Azure Policy hozzáfűzi a hatás részleteiben megadott tömböt. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `modify``add`művelettel | Azure Policy hozzáfűzi a hatás részleteiben megadott tömböt. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `modify``addOrReplace`művelettel | Azure Policy eltávolítja az összes meglévő tömbbeli tagot, és hozzáfűzi a hatás részletei között megadott tömböt. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `append` | Azure Policy hozzáfűz egy értéket az `action` egyes tömb tagjainak tulajdonságához. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `modify``add`művelettel | Azure Policy hozzáfűz egy értéket az `action` egyes tömb tagjainak tulajdonságához. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `modify``addOrReplace`művelettel | Azure Policy hozzáfűzi vagy lecseréli az `action` egyes tömb tagjainak meglévő tulajdonságát. |

További információ: [hozzáfűzési példák](../concepts/effects.md#append-examples).

## <a name="appendix--additional--alias-examples"></a>Függelék – további [*] alias-példák

Azt javasoljuk, hogy a [mezők száma kifejezések](#field-count-expressions) használatával győződjön meg arról, hogy a kérelem tartalmában található tömb tagjainak egyike vagy bármelyike feltételnek felel-e meg. Bizonyos egyszerű feltételek esetében azonban lehetséges, hogy ugyanazt az eredményt egy tömb-alias használatával ( [a tömb tagjainak gyűjteményével kapcsolatos hivatkozásban](#referencing-the-array-members-collection)leírt módon) is elérheti. Ez olyan házirend-szabályok esetében lehet hasznos, amelyek túllépik a megengedett **számú** kifejezések korlátját. Példák gyakori használati esetekre:

Az alábbi forgatókönyv-táblázathoz tartozó példa házirend-szabály:

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            <-- Condition (see table below) -->
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

A **ipRules** tömb az alábbi forgatókönyv-táblázat esetében a következő:

```json
"ipRules": [
    {
        "value": "127.0.0.1",
        "action": "Allow"
    },
    {
        "value": "192.168.1.1",
        "action": "Allow"
    }
]
```

Az alábbi példában szereplő összes feltételnél cserélje le a következőt: `<field>` `"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"` .

A következő eredmények a feltétel és a példaként megadott házirend-szabály kombinációjának eredményei, valamint a fenti meglévő értékek tömbje:

|Feltétel |Eredmény | Eset |Magyarázat |
|-|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |Semmit |Nincs egyezés |Az egyik tömb elem hamis (127.0.0.1! = 127.0.0.1) és egy True (127.0.0.1! = 192.168.1.1) értéket ad vissza, így a **notEquals** feltétel _hamis_ , és a hatás nincs aktiválva. |
|`{<field>,"notEquals":"10.0.4.1"}` |Házirend hatása |Nincs egyezés |Mindkét tömb elem igaz értéket (10.0.4.1! = 127.0.0.1 és 10.0.4.1! = 192.168.1.1) is kiértékel, így a **notEquals** feltétel _igaz_ , és a hatás aktiválódik. |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |Házirend hatása |Egy vagy több egyezés |Az egyik tömb elem hamis (127.0.0.1! = 127.0.0.1) és egy True (127.0.0.1! = 192.168.1.1) értéket ad vissza, így a **notEquals** feltétel _hamis_. A logikai operátor igaz (**nem** _hamis) értéket_ ad vissza, ezért a hatás aktiválódik. |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |Semmit |Egy vagy több egyezés |Mindkét tömb elem igaz értéket (10.0.4.1! = 127.0.0.1 és 10.0.4.1! = 192.168.1.1) is kiértékel, így a **notEquals** feltétel _igaz_. A logikai operátor hamis (**nem** _igaz_) értéket ad vissza, ezért a hatás nincs aktiválva. |
|`"not":{<field>,"Equals":"127.0.0.1"}` |Házirend hatása |Nem minden egyezés |Az egyik tömb elem igaz értéket (127.0.0.1 = = 127.0.0.1) és egy hamis (127.0.0.1 = = 192.168.1.1) értéket ad vissza, így az **Equals** feltétel _hamis_. A logikai operátor igaz (**nem** _hamis) értéket_ ad vissza, ezért a hatás aktiválódik. |
|`"not":{<field>,"Equals":"10.0.4.1"}` |Házirend hatása |Nem minden egyezés |A tömb elemeinek értéke false (10.0.4.1 = = 127.0.0.1 és 10.0.4.1 = = 192.168.1.1), így az **Equals** feltétel _hamis_. A logikai operátor igaz (**nem** _hamis) értéket_ ad vissza, ezért a hatás aktiválódik. |
|`{<field>,"Equals":"127.0.0.1"}` |Semmit |Összes egyezés |Az egyik tömb elem igaz értéket (127.0.0.1 = = 127.0.0.1) és egy hamis (127.0.0.1 = = 192.168.1.1) értéket ad vissza, így az **egyenlő** állapot _hamis_ , és a hatás nem aktiválódik. |
|`{<field>,"Equals":"10.0.4.1"}` |Semmit |Összes egyezés |Mindkét tömb elem hamis (10.0.4.1 = = 127.0.0.1 és 10.0.4.1 = = 192.168.1.1) értéket ad eredményként, így az **egyenlő** állapot _hamis_ , és a hatás nem aktiválódik. |

## <a name="next-steps"></a>Következő lépések

- Tekintse át a példákat [Azure Policy mintákon](../samples/index.md).
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](../concepts/effects.md).
- Megtudhatja, hogyan [hozhat létre programozott módon házirendeket](programmatically-create.md).
- Ismerje meg, hogyan javíthatja a [nem megfelelő erőforrásokat](remediate-resources.md).
- Tekintse át, hogy a felügyeleti csoport hogyan [rendezi az erőforrásokat az Azure felügyeleti csoportjaival](../../management-groups/overview.md).

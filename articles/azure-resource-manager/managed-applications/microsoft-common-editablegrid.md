---
title: EditableGrid FELHASZNÁLÓIFELÜLET-elem
description: A Azure Portal Microsoft. Common. EditableGrid felhasználói felületi elemének ismertetése. Lehetővé teszi, hogy a felhasználók táblázatos bevitelt gyűjtsenek.
author: tfitzmac
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: tomfitz
ms.openlocfilehash: 04f86883a75110985d1cbe050fe3fd3e0582986a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88893773"
---
# <a name="microsoftcommoneditablegrid-ui-element"></a>Microsoft. Common. EditableGrid felhasználói felületi elem

Táblázatos bevitel összegyűjtésének vezérlője. A rácson belüli összes mező szerkeszthető, és a sorok száma eltérő lehet.

## <a name="ui-sample"></a>Felhasználói felület mintája

:::image type="content" source="./media/managed-application-elements/microsoft-common-editablegrid.png" alt-text="Microsoft. Common. EditableGrid":::

## <a name="schema"></a>Séma

```json
{
  "name": "people",
  "type": "Microsoft.Common.EditableGrid",
  "ariaLabel": "Enter information per person",
  "label": "People",
  "constraints": {
    "width": "Full",
    "rows": {
      "count": {
        "min": 1,
        "max": 10
      }
    },
    "columns": [
      {
        "id": "colName",
        "header": "Name",
        "width": "1fr",
        "element": {
          "type": "Microsoft.Common.TextBox",
          "placeholder": "Full name",
          "constraints": {
            "required": true,
            "validations": [
              {
                "isValid": "[startsWith(last(take(steps('grid').people, $rowIndex)).colName, 'contoso')]",
                "message": "Must start with 'contoso'."
              },
              {
                "regex": "^[a-z0-9A-Z]{1,30}$",
                "message": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
              }
            ]
          }
        }
      },
      {
        "id": "colGender",
        "header": "Gender",
        "width": "1fr",
        "element": {
          "name": "dropDown1",
          "type": "Microsoft.Common.DropDown",
          "placeholder": "Select a gender...",
          "constraints": {
            "allowedValues": [
              {
                "label": "Female",
                "value": "female"
              },
              {
                "label": "Male",
                "value": "male"
              },
              {
                "label": "Other",
                "value": "other"
              }
            ],
            "required": true
          }
        }
      },
      {
        "id": "colContactPreference",
        "header": "Contact preference",
        "width": "1fr",
        "element": {
          "type": "Microsoft.Common.OptionsGroup",
          "constraints": {
            "allowedValues": [
              {
                "label": "Email",
                "value": "email"
              },
              {
                "label": "Text",
                "value": "text"
              }
            ],
            "required": true
          }
        }
      }
    ]
  }
}
```

## <a name="sample-output"></a>Példakimenet

```json
{
  "colName": "contoso",
  "colGender": "female",
  "colContactPreference": "email"
}
```

## <a name="remarks"></a>Megjegyzések

- Az oszlopok tömbben csak a [szövegmező](microsoft-common-textbox.md), a [OptionsGroup](microsoft-common-optionsgroup.md)és a [legördülő lista](microsoft-common-dropdown.md)érvényes.
- A `$rowIndex` változó csak a rács oszlopainak gyermekein belül található kifejezésekben érvényes. Ez egy egész szám, amely az elem relatív sorainak indexét jelöli, és a darabszám eggyel kezdődik, és eggyel nő. Ahogy az a séma szakaszban is látható `"columns":` , az `$rowIndex` érvényesítésre szolgál.
- Ha az érvényesítést a változó használatával hajtja végre `$rowIndex` , lehetséges, hogy az aktuális sor értékét a és a parancsok kombinálásával lehet beolvasni `last()` `take()` .

  Például:

  `last(take(<reference_to_grid>, $rowIndex))`

- A `label` tulajdonság nem jelenik meg a vezérlő részeként, de a végső lapon Összefoglalva jelenik meg.
- A `ariaLabel` tulajdonság a rács kisegítő címkéje. A képernyőolvasót használó felhasználók számára hasznos szöveg megadása.
- A `constraints.width` tulajdonság a rács teljes szélességének megadására szolgál. A lehetőségek _megteltek_, _közepes_ és _kicsik_. Az alapértelmezett érték _megtelt_.
- Az `width` oszlopok gyermekein lévő tulajdonság határozza meg az oszlopszélességet. A szélességeket olyan tört egységekkel határozzák meg, mint a _3FR_, és az oszlopokhoz arányos teljes terület van kiválasztva. Ha nincs megadva oszlopszélesség, az alapértelmezett érték a _1fr_.

## <a name="next-steps"></a>Következő lépések

- A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
- A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).

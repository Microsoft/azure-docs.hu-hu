---
title: Legördülő KEZELŐFELÜLETi elem
description: A cikk a Azure Portal Microsoft. Common. DropDown felhasználói felületi elemét ismerteti. A használatával választható lehetőségek közül választhat a felügyelt alkalmazások telepítésekor.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: tomfitz
ms.openlocfilehash: b9b27a432776635290c7e8e796e84d8c1e0e8675
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92168341"
---
# <a name="microsoftcommondropdown-ui-element"></a>Microsoft. Common. DropDown felhasználói felületi elem

Egy legördülő listával rendelkező kiválasztási vezérlő. Csak egyetlen vagy több elem kijelölését engedélyezheti. Igény szerint az elemekhez leírást is megadhat.

## <a name="ui-sample"></a>Felhasználói felület mintája

A legördülő elem különböző lehetőségeket tartalmaz, amelyek meghatározzák a megjelenést a portálon.

Ha csak egyetlen elem van engedélyezve a kijelöléshez, a vezérlő a következőképpen jelenik meg:

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-1.png" alt-text="Microsoft. Common. DropDown egyetlen kijelölés":::

Ha a leírások szerepelnek, a vezérlő a következőképpen jelenik meg:

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-2.png" alt-text="Microsoft. Common. DropDown egyetlen kijelölés a leírásokkal":::

Ha a többszörös kijelölés engedélyezve van, a vezérlő hozzáadja az **összes kijelölése** lehetőséget és a jelölőnégyzeteket több elem kiválasztásához:

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-3.png" alt-text="Microsoft. Common. DropDown többszörös kijelölés":::

A többszörös kijelölés engedélyezése esetén a leírások is szerepelhetnek.

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-4.png" alt-text="Képernyőkép, amely bemutatja, hogyan szerepelhetnek a leírások a többszörös kijelölés engedélyezése esetén":::

Ha a Szűrés engedélyezve van, a vezérlő tartalmaz egy szövegmezőt a szűrési érték hozzáadásához.

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-5.png" alt-text="Microsoft. Common. DropDown többszörös kijelölés a leírásokkal":::

## <a name="schema"></a>Séma

```json
{
    "name": "element1",
    "type": "Microsoft.Common.DropDown",
    "label": "Example drop down",
    "placeholder": "",
    "defaultValue": "Value two",
    "toolTip": "",
    "multiselect": true,  
    "selectAll": true,  
    "filter": true,  
    "filterPlaceholder": "Filter items ...",  
    "multiLine": true,  
    "defaultDescription": "A value for selection",  
    "constraints": {
        "allowedValues": [
            {
                "label": "Value one",
                "description": "The value to select for option 1.",
                "value": "one"
            },
            {
                "label": "Value two",
                "description": "The value to select for option 2.",
                "value": "two"
            }
        ],
        "required": true
    },
    "visible": true
}
```

## <a name="sample-output"></a>Példakimenet

```json
"two"
```

## <a name="remarks"></a>Megjegyzések

- Ezzel `multiselect` a beállítással adhatja meg, hogy a felhasználók kijelölhetnek-e egynél több elemet.
- Alapértelmezés szerint `selectAll` a `true` többszörös kijelölés engedélyezve van.
- A `filter` tulajdonság lehetővé teszi a felhasználók számára, hogy a lehetőségek hosszú listáján belül keressenek.
- A címkéje `constraints.allowedValues` az elem megjelenített szövege, a értéke pedig az elem kimeneti értéke, ha ki van választva.
- Ha meg van adva, az alapértelmezett értéknek szerepelnie kell a címkében `constraints.allowedValues` . Ha nincs megadva, az első elem `constraints.allowedValues` van kiválasztva. Az alapértelmezett érték **Null**.
- `constraints.allowedValues` legalább egy elemmel kell rendelkeznie.
- Ahhoz, hogy egy értéket ne lehessen emulálni, adjon hozzá egy címkével ellátott és `""` (üres karakterlánc) értéket a következőhöz: `constraints.allowedValues` .
- A `defaultDescription` tulajdonság olyan elemekhez használatos, amelyek nem rendelkeznek leírással.
- A `placeholder` tulajdonság egy olyan súgószöveg, amely eltűnik, amikor a felhasználó megkezdi a szerkesztést. Ha a `placeholder` és `defaultValue` mindkettő is meg van adva, a `defaultValue` veszi elsőbbséget, és megjelenik.

## <a name="next-steps"></a>Következő lépések

* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).

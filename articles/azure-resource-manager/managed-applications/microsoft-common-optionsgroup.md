---
title: OptionsGroup FELHASZNÁLÓIFELÜLET-elem
description: A Azure Portal Microsoft. Common. OptionsGroup felhasználói felületi elemének ismertetése. Lehetővé teszi, hogy a felhasználók a felügyelt alkalmazások telepítésekor a rendelkezésre álló lehetőségek közül választhatnak.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tomfitz
ms.openlocfilehash: aa73b4cbded98291a14792a7151df9fdfb885b53
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87004190"
---
# <a name="microsoftcommonoptionsgroup-ui-element"></a>Microsoft. Common. OptionsGroup felhasználói felületi elem

A OptionsGroup vezérlőelem lehetővé teszi, hogy a felhasználók két vagy több lehetőség közül válasszanak ki egyet. Egy felhasználó csak egy lehetőséget választhat ki.

> [!NOTE]
> A múltban ez a vezérlő vízszintesen jeleníti meg a beállításokat. A vezérlő mostantól a választógombok függőlegesen jeleníti meg a beállításokat.

## <a name="ui-sample"></a>Felhasználói felület mintája

:::image type="content" source="./media/managed-application-elements/microsoft-common-optionsgroup-2.png" alt-text="Microsoft.Common.OptionsGroup":::

## <a name="schema"></a>Séma

```json
{
  "name": "element1",
  "type": "Microsoft.Common.OptionsGroup",
  "label": "Some options group",
  "defaultValue": "Value two",
  "toolTip": "",
  "constraints": {
    "allowedValues": [
      {
        "label": "Value one",
        "value": "one"
      },
      {
        "label": "Value two",
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

- A címkéje `constraints.allowedValues` az elem megjelenített szövege, a értéke pedig az elem kimeneti értéke, ha ki van választva.
- Ha meg van adva, az alapértelmezett értéknek szerepelnie kell a címkében `constraints.allowedValues` . Ha nincs megadva, alapértelmezés szerint az első elem `constraints.allowedValues` van kiválasztva. Az alapértelmezett érték **Null**.
- `constraints.allowedValues` legalább egy elemmel kell rendelkeznie.

## <a name="next-steps"></a>Következő lépések

* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).

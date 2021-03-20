---
title: Csúszka felhasználói felületének eleme
description: Ismerteti a Microsoft. Common. Slider FELHASZNÁLÓIFELÜLET-elemét Azure Portal. Lehetővé teszi a felhasználók számára egy érték beállítását a különböző lehetőségek közül.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/10/2020
ms.author: tomfitz
ms.openlocfilehash: bb168a4bff6d2570f539307530232b5063bb535c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87098536"
---
# <a name="microsoftcommonslider-ui-element"></a>Microsoft. Common. Slider FELHASZNÁLÓIFELÜLET-elem

A csúszka vezérlőelem lehetővé teszi a felhasználók számára, hogy az engedélyezett értékek egy tartományába legyenek kiválasztva.

## <a name="ui-sample"></a>Felhasználói felület mintája

:::image type="content" source="./media/managed-application-elements/microsoft-common-slider.png" alt-text="Microsoft. Common. Slider":::

## <a name="schema"></a>Séma

```json
{
    "name": "memorySize",
    "type": "Microsoft.Common.Slider",
    "min": 1,
    "max": 64,
    "label": "Memory",
    "subLabel": "MB",
    "defaultValue": 24,
    "showStepMarkers": false,
    "toolTip": "Pick the size in MB",
    "constraints": {
        "required": false
    },
    "visible": true
}
```

## <a name="sample-output"></a>Példakimenet

```json
26
```

## <a name="remarks"></a>Megjegyzések

- A `min` és az `max` értékek megadása kötelező. A csúszka indítási és végponti pontjait adják meg.
- A `showStepMarkers` tulajdonság értéke TRUE (igaz). A lépés jelölők csak akkor jelennek meg, ha a min és a Max közötti tartomány 100 vagy annál kisebb.


## <a name="next-steps"></a>Következő lépések

* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).

---
title: Jelölőnégyzet felhasználói felület elem
description: A cikk a Microsoft. Common. CheckBox felhasználói felület elemét írja le Azure Portal. Lehetővé teszi a felhasználók számára, hogy jelöljenek ki egy beállítást, vagy törölje a jelölését.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tomfitz
ms.openlocfilehash: 9f40f223cca34df58d2af7373d8f60fd7f383162
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87098548"
---
# <a name="microsoftcommoncheckbox-ui-element"></a>Microsoft. Common. CheckBox felhasználói felület elem

A jelölőnégyzet vezérlőelem lehetővé teszi, hogy a felhasználók ellenőrizzék vagy töröljenek egy beállítást. A vezérlő **igaz** értéket ad vissza, ha a vezérlő be van jelölve, vagy **hamis** , ha nincs bejelölve.

## <a name="ui-sample"></a>Felhasználói felület mintája

:::image type="content" source="./media/managed-application-elements/microsoft-common-checkbox.png" alt-text="Microsoft. Common. CheckBox":::

## <a name="schema"></a>Séma

```json
{
    "name": "legalAccept",
    "type": "Microsoft.Common.CheckBox",
    "label": "I agree to the terms and conditions.",
    "constraints": {
        "required": true,
        "validationMessage": "Please acknowledge the legal conditions."
    }
}
```

## <a name="sample-output"></a>Példakimenet

```json
true
```

## <a name="remarks"></a>Megjegyzések

Ha a **kötelező** **igaz** értéket állítja be, a felhasználónak be kell jelölnie a jelölőnégyzetet. Ha a felhasználó nem jelöli be a jelölőnégyzetet, az érvényesítési üzenet jelenik meg.

## <a name="next-steps"></a>Következő lépések

* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).

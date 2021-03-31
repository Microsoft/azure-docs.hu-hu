---
title: TagsByResource FELHASZNÁLÓIFELÜLET-elem
description: A Azure Portal Microsoft. Common. TagsByResource felhasználói felületi elemének ismertetése. A használatával címkéket alkalmazhat az erőforrásokra az üzembe helyezés során.
author: tfitzmac
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: tomfitz
ms.openlocfilehash: e730201812005a9b469a964e4acd081ebe86b100
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87063929"
---
# <a name="microsoftcommontagsbyresource-ui-element"></a>Microsoft. Common. TagsByResource felhasználói felületi elem

A [címkék](../management/tag-resources.md) egy központi telepítésben lévő erőforrásokkal való társítására szolgáló vezérlő.

## <a name="ui-sample"></a>Felhasználói felület mintája

![Microsoft.Common.DropDown](./media/managed-application-elements/microsoft-common-tagsbyresource.png)

## <a name="schema"></a>Séma

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TagsByResource",
  "resources": [
    "Microsoft.Storage/storageAccounts",
    "Microsoft.Compute/virtualMachines"
  ]
}
```

## <a name="sample-output"></a>Példakimenet

```json
{
  "Microsoft.Storage/storageAccounts": {
    "Dept": "Finance",
    "Environment": "Production"
  },
  "Microsoft.Compute/virtualMachines": {
    "Dept": "Finance"
  }
}
```

## <a name="remarks"></a>Megjegyzések

- A tömb legalább egy elemét `resources` meg kell adni.
- A minden elemének `resources` teljesen minősített erőforrástípus kell lennie. Ezek az elemek az **erőforrás** legördülő menüben jelennek meg, és a felhasználó taggable.
- A vezérlő kimenete úgy van formázva, hogy könnyen lehessen hozzárendelni a címke értékeit egy Azure Resource Manager sablonban. Ha a vezérlő kimenetét szeretné fogadni egy sablonban, adjon meg egy paramétert a sablonban az alábbi példában látható módon:

  ```json
  "parameters": {
    "tagsByResource": { "type": "object", "defaultValue": {} }
  }
  ```

  Minden megcímkézhető erőforráshoz rendelje hozzá a címkék tulajdonságot az erőforrástípus paraméterének értékéhez:

  ```json
  {
    "name": "saName1",
    "type": "Microsoft.Storage/storageAccounts",
    "tags": "[ if(contains(parameters('tagsByResource'), 'Microsoft.Storage/storageAccounts'), parameters('tagsByResource')['Microsoft.Storage/storageAccounts'], json('{}')) ]",
    ...
  ```

- Használja az [IF](../templates/template-functions-logical.md#if) függvényt a tagsByResource paraméter elérésekor. Lehetővé teszi, hogy üres objektumot rendeljen hozzá, ha nem rendel hozzá címkéket a megadott erőforrás-típushoz.

## <a name="next-steps"></a>Következő lépések

- A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
- A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).

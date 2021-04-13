---
title: ArmApiControl FELHASZNÁLÓIFELÜLET-elem
description: A Azure Portal Microsoft. Solutions. ArmApiControl felhasználói felületi elemének ismertetése. API-műveletek meghívásához használatos.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: tomfitz
ms.openlocfilehash: fdc299ef1945e3ee0810f1c314fc07edfb4f4873
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313300"
---
# <a name="microsoftsolutionsarmapicontrol-ui-element"></a>Microsoft. Solutions. ArmApiControl FELHASZNÁLÓIFELÜLET-elem

A ArmApiControl lehetővé teszi egy Azure Resource Manager API-művelet eredményének beolvasását. A dinamikus tartalom más vezérlőkben való feltöltéséhez használja az eredményeket.

## <a name="ui-sample"></a>Felhasználói felület mintája

Ehhez a vezérlőhöz nincs felhasználói felület.

## <a name="schema"></a>Séma

A következő példában a vezérlő sémája látható:

```json
{
    "name": "testApi",
    "type": "Microsoft.Solutions.ArmApiControl",
    "request": {
        "method": "{HTTP-method}",
        "path": "{path-for-the-URL}",
        "body": {
            "key1": "val1",
            "key2": "val2"
        }
    }
}
```

## <a name="sample-output"></a>Példakimenet

A vezérlő kimenete nem jelenik meg a felhasználó számára. Ehelyett a művelet eredményét más vezérlők használják.

## <a name="remarks"></a>Megjegyzések

- A `request.method` tulajdonság a http-metódust adja meg. Csak `GET` vagy `POST` engedélyezett.
- A `request.path` tulajdonság olyan URL-címet ad meg, amelynek az ARM-végpont relatív elérési útjának kell lennie. Ez lehet statikus elérési út, vagy dinamikusan is felépíthető a többi vezérlő kimeneti értékeire hivatkozva.

  Például egy ARM-hívás erőforrás- `Microsoft.Network/expressRouteCircuits` szolgáltatónak:

  ```json
  "path": "<subid>/resourceGroup/<resourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<routecircuitName>/?api-version=2020-05-01"
  ```

- A `request.body` tulajdonság megadása nem kötelező. Ezzel a beállítással adhatja meg a kéréssel elküldhető JSON-törzset. A törzs statikus tartalom lehet, vagy dinamikusan felépíthető, ha más vezérlőkből származó kimeneti értékekre hivatkozik.

## <a name="example"></a>Példa

A következő példában az `providersApi` elem meghívja az API-t, hogy lekérje a szolgáltatói objektumok tömbjét.

Az `allowedValues` elem tulajdonsága úgy `providersDropDown` van konfigurálva, hogy beolvassa a szolgáltatók nevét. Megjeleníti őket a legördülő listában.

```json
{
    "name": "providersApi",
    "type": "Microsoft.Solutions.ArmApiControl",
    "request": {
        "method": "GET",
        "path": "[concat(subscription().id, '/providers/Microsoft.Network/expressRouteServiceProviders?api-version=2019-02-01')]"
    }
},
{
    "name": "providerDropDown",
    "type": "Microsoft.Common.DropDown",
    "label": "Provider",
    "toolTip": "The provider that offers the express route connection.",
    "constraints": {
        "allowedValues": "[map(steps('settings').providersApi.value, (item) => parse(concat('{\"label\":\"', item.name, '\",\"value\":\"', item.name, '\"}')))]",
        "required": true
    },
    "visible": true
}
```

A ArmApiControl egy erőforrás-név rendelkezésre állásának ellenőrzését bemutató példát a [Microsoft. Common. szövegmezőben](microsoft-common-textbox.md)talál.

## <a name="next-steps"></a>Következő lépések

- A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
- A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).

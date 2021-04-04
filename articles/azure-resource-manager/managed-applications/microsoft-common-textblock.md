---
title: TextBlock FELHASZNÁLÓIFELÜLET-elem
description: A Azure Portal Microsoft. Common. TextBlock felhasználói felületi elemének ismertetése. A paranccsal adhat hozzá szöveget az illesztőfelülethez.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: ba2c4d410891910c29ee1fda1065f8230ab171bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87063861"
---
# <a name="microsoftcommontextblock-ui-element"></a>Microsoft. Common. TextBlock felhasználói felületi elem

Egy vezérlőelem, amellyel szöveget adhat hozzá a portál felületéhez.

## <a name="ui-sample"></a>Felhasználói felület mintája

![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft-common-textblock.png)

## <a name="schema"></a>Séma

```json
{
  "name": "text1",
  "type": "Microsoft.Common.TextBlock",
  "visible": true,
  "options": {
    "text": "Please provide the configuration values for your application.",
    "link": {
      "label": "Learn more",
      "uri": "https://www.microsoft.com"
    }
  }
}
```

## <a name="sample-output"></a>Példakimenet

```json
"Please provide the configuration values for your application. Learn more"
```

## <a name="next-steps"></a>Következő lépések

* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).

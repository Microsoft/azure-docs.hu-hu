---
title: PublicIpAddressCombo FELHASZNÁLÓIFELÜLET-elem
description: A Azure Portal Microsoft. Network. PublicIpAddressCombo felhasználói felületi elemének ismertetése.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 5def6db9d551b3882204c9f997f164a0df7ac223
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87063282"
---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>Microsoft. Network. PublicIpAddressCombo FELHASZNÁLÓIFELÜLET-elem

Új vagy meglévő nyilvános IP-cím kiválasztására szolgáló vezérlők csoportja.

## <a name="ui-sample"></a>Felhasználói felület mintája

![Microsoft.Network.PublicIpAddressCombo](./media/managed-application-elements/microsoft-network-publicipaddresscombo.png)

- Ha a felhasználó a "nincs" lehetőséget választja a nyilvános IP-címhez, a tartománynév feliratának szövegmezője rejtett.
- Ha a felhasználó kiválaszt egy meglévő nyilvános IP-címet, a tartománynév felirat szövegmező le lesz tiltva. Értéke a kijelölt IP-cím tartománynév-címkéje.
- A tartománynév utótagja (például westus.cloudapp.azure.com) automatikusan frissül a kiválasztott hely alapján.

## <a name="schema"></a>Séma

```json
{
  "name": "element1",
  "type": "Microsoft.Network.PublicIpAddressCombo",
  "label": {
    "publicIpAddress": "Public IP address",
    "domainNameLabel": "Domain name label"
  },
  "toolTip": {
    "publicIpAddress": "",
    "domainNameLabel": ""
  },
  "defaultValue": {
    "publicIpAddressName": "ip01",
    "domainNameLabel": "mydomain"
  },
  "constraints": {
    "required": {
      "domainNameLabel": true
    }
  },
  "options": {
    "hideNone": false,
    "hideDomainNameLabel": false,
    "hideExisting": false,
    "zone": 3
  },
  "visible": true
}
```

## <a name="sample-output"></a>Példakimenet

Ha a felhasználó nem választ nyilvános IP-címet, a vezérlő a következő kimenetet adja vissza:

```json
{
  "newOrExistingOrNone": "none"
}
```

Ha a felhasználó új vagy meglévő IP-címet választ, a vezérlő a következő kimenetet adja vissza:

```json
{
  "name": "ip01",
  "resourceGroup": "rg01",
  "domainNameLabel": "mydomain",
  "publicIPAllocationMethod": "Dynamic",
  "sku": "Basic",
  "newOrExistingOrNone": "new"
}
```

- Ha `options.hideNone` a értéke **true (igaz**), `newOrExistingOrNone` akkor csak az **új** vagy a **meglévő** értéket fogja megadni.
- Ha `options.hideDomainNameLabel` a értéke **true (igaz**), `domainNameLabel` akkor nincs deklarálva.

## <a name="remarks"></a>Megjegyzések

- Ha a `constraints.required.domainNameLabel` értéke **true (igaz**), a felhasználónak meg kell adnia egy tartománynév címkét új nyilvános IP-cím létrehozásakor. Egy címke nélküli meglévő nyilvános IP-címek nem választhatók ki a kijelöléshez.
- Ha a `options.hideNone` értéke **true (igaz**), akkor a nyilvános IP-cím **egyikének** kiválasztására szolgáló beállítás rejtett. Az alapértelmezett érték: **hamis**.
- Ha a `options.hideDomainNameLabel` értéke **true (igaz**), akkor a tartománynév feliratának szövegmezője rejtett. Az alapértelmezett érték: **hamis**.
- Ha az `options.hideExisting` értéke igaz, a felhasználó nem választhat meglévő nyilvános IP-címet. Az alapértelmezett érték: **hamis**.
- A esetében `zone` csak a megadott zónához vagy zónához tartozó nyilvános IP-címek érhetők el.

## <a name="next-steps"></a>Következő lépések

* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).

---
title: VirtualNetworkCombo FELHASZNÁLÓIFELÜLET-elem
description: A Azure Portal Microsoft. Network. VirtualNetworkCombo felhasználói felületi elemének ismertetése.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 711f5293b205c1f500c6d9e08154342285ef959b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87033204"
---
# <a name="microsoftnetworkvirtualnetworkcombo-ui-element"></a>Microsoft. Network. VirtualNetworkCombo FELHASZNÁLÓIFELÜLET-elem

Új vagy meglévő virtuális hálózat kiválasztására szolgáló vezérlők csoportja.

## <a name="ui-sample"></a>Felhasználói felület mintája

Ha a felhasználó új virtuális hálózatot választ, a felhasználó testreszabhatja az egyes alhálózatok nevét és a hozzá tartozó előtagot. Az alhálózatok konfigurálása nem kötelező.

![Microsoft. Network. VirtualNetworkCombo új](./media/managed-application-elements/microsoft-network-virtualnetworkcombo-new.png)

Ha a felhasználó egy meglévő virtuális hálózatot vesz fel, a felhasználónak le kell képeznie az egyes alhálózatokat, amelyekre a központi telepítési sablonnak szüksége van egy meglévő alhálózathoz. Ebben az esetben szükség van az alhálózatok konfigurálására.

![Microsoft. Network. VirtualNetworkCombo meglévő](./media/managed-application-elements/microsoft-network-virtualnetworkcombo-existing.png)

## <a name="schema"></a>Séma

```json
{
  "name": "element1",
  "type": "Microsoft.Network.VirtualNetworkCombo",
  "label": {
    "virtualNetwork": "Virtual network",
    "subnets": "Subnets"
  },
  "toolTip": {
    "virtualNetwork": "",
    "subnets": ""
  },
  "defaultValue": {
    "name": "vnet01",
    "addressPrefixSize": "/16"
  },
  "constraints": {
    "minAddressPrefixSize": "/16"
  },
  "options": {
    "hideExisting": false
  },
  "subnets": {
    "subnet1": {
      "label": "First subnet",
      "defaultValue": {
        "name": "subnet-1",
        "addressPrefixSize": "/24"
      },
      "constraints": {
        "minAddressPrefixSize": "/24",
        "minAddressCount": 12,
        "requireContiguousAddresses": true
      }
    },
    "subnet2": {
      "label": "Second subnet",
      "defaultValue": {
        "name": "subnet-2",
        "addressPrefixSize": "/26"
      },
      "constraints": {
        "minAddressPrefixSize": "/26",
        "minAddressCount": 8,
        "requireContiguousAddresses": true
      }
    }
  },
  "visible": true
}
```

## <a name="sample-output"></a>Példakimenet

```json
{
  "name": "vnet01",
  "resourceGroup": "rg01",
  "addressPrefixes": ["10.0.0.0/16"],
  "newOrExisting": "new",
  "subnets": {
    "subnet1": {
      "name": "subnet-1",
      "addressPrefix": "10.0.0.0/24",
      "startAddress": "10.0.0.1"
    },
    "subnet2": {
      "name": "subnet-2",
      "addressPrefix": "10.0.1.0/26",
      "startAddress": "10.0.1.1"
    }
  }
}
```

## <a name="remarks"></a>Megjegyzések

- Ha meg van adva, a rendszer az első nem átfedésben lévő címzési előtagot `defaultValue.addressPrefixSize` automatikusan meghatározza a felhasználó előfizetésében lévő virtuális hálózatok alapján.
- A és az alapértelmezett `defaultValue.name` értéke `defaultValue.addressPrefixSize` **Null**.
- `constraints.minAddressPrefixSize` meg kell adni. A megadott értéknél kisebb címtartományt tartalmazó meglévő virtuális hálózatok nem választhatók ki a kijelöléshez.
- `subnets` meg kell adni, és `constraints.minAddressPrefixSize` minden alhálózathoz meg kell adni.
- Új virtuális hálózat létrehozásakor az egyes alhálózatok címének előtagját a rendszer automatikusan kiszámítja a virtuális hálózat címének előtagja és a megfelelő alapján `addressPrefixSize` .
- Meglévő virtuális hálózat használata esetén a megfelelőnél kisebb alhálózatok nem `constraints.minAddressPrefixSize` érhetők el a kiválasztáshoz. Emellett, ha meg van adva, a legalább elérhető címekkel nem rendelkező alhálózatok nem `minAddressCount` érhetők el a kijelöléshez. Az alapértelmezett érték a **0**. Ha biztosítani szeretné, hogy az elérhető címek folytonos legyenek, a **igaz** értéket kell megadni a következőhöz: `requireContiguousAddresses` . Az alapértelmezett érték **true (igaz**).
- Az alhálózatok meglévő virtuális hálózatban való létrehozása nem támogatott.
- Ha `options.hideExisting` az értéke **igaz**, a felhasználó nem választhat meglévő virtuális hálózatot. Az alapértelmezett érték: **hamis**.

## <a name="next-steps"></a>Következő lépések

* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).

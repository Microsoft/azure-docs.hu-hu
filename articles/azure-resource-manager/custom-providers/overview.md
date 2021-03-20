---
title: Egyéni szolgáltatók áttekintése
description: Ismerje meg az Azure-beli egyéni erőforrás-szolgáltatókat, valamint azt, hogyan bővítheti az Azure API-síkot a munkafolyamatok méretének megfelelően.
author: jjbfour
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 68b8bd187d58cd71778b8a922684cc3817a0715d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "80398475"
---
# <a name="azure-custom-resource-providers-overview"></a>Az Azure egyéni erőforrás-szolgáltatók áttekintése

Az Azure egyéni erőforrás-szolgáltatók egy bővíthető platform az Azure-hoz. Lehetővé teszi egyéni API-k definiálását, amelyek segítségével bővíthető az alapértelmezett Azure-élmény. Ez a dokumentáció a következőket ismerteti:

- Azure-beli egyéni erőforrás-szolgáltató létrehozása és üzembe helyezése.
- Az Azure egyéni erőforrás-szolgáltatóinak használata a meglévő munkafolyamatok kiterjesztéséhez.
- Hol találhatók útmutatók és kód minták az első lépésekhez.

![Az egyéni szolgáltató áttekintése](./media/overview/overview.png)

> [!IMPORTANT]
> Az egyéni szolgáltatók jelenleg nyilvános előzetes verzióban érhetők el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-can-custom-resource-providers-do"></a>Mit tehet az egyéni erőforrás-szolgáltatók?

Íme néhány példa arra, hogy mit érhet el az Azure egyéni erőforrás-szolgáltatókkal:

- A belső és külső szolgáltatások belefoglalásához bővítse ki Azure Resource Manager REST API.
- Egyéni forgatókönyvek engedélyezése a meglévő Azure-munkafolyamatok felett.
- Testreszabhatja Azure Resource Manager sablonok vezérlőjét és hatását.

## <a name="what-is-a-custom-resource-provider"></a>Mi az egyéni erőforrás-szolgáltató?

Az Azure egyéni erőforrás-szolgáltatói az Azure és a végpont közötti szerződés létrehozásával készülnek. Ez a szerződés egy új erőforrás, a **Microsoft. CustomProviders/resourceProviders** használatával határozza meg az új erőforrások és műveletek listáját. Az egyéni erőforrás-szolgáltató ezután elérhetővé teszi ezeket az új API-kat az Azure-ban. Az Azure egyéni erőforrás-szolgáltató három részből áll: az egyéni erőforrás-szolgáltatóból, a **végpontokból** és az egyéni erőforrásokból.

## <a name="how-to-build-custom-resource-providers"></a>Egyéni erőforrás-szolgáltatók létrehozása

Az egyéni erőforrás-szolgáltatók az Azure és a végpontok közötti szerződések listája. Ez a szerződés azt ismerteti, hogy az Azure hogyan működjön együtt egy végponttal. Az erőforrás-szolgáltató úgy viselkedik, mint egy proxy, és továbbítja a kéréseket és a válaszokat a megadott **végpontra**. Egy erőforrás-szolgáltató két típusú szerződést tud megadni: [**resourceTypes**](./custom-providers-resources-endpoint-how-to.md) és [**műveletek**](./custom-providers-action-endpoint-how-to.md). Ezek a végpont-definíciók segítségével engedélyezettek. A végpontok definíciója három mezőből áll: **név**, **routingType** és **végpont**.

Példa végpontra:

```JSON
{
  "name": "{endpointDefinitionName}",
  "routingType": "Proxy",
  "endpoint": "https://{endpointURL}/"
}
```

Tulajdonság | Kötelező | Leírás
---|---|---
name | *igen* | A végpont definíciójának neve. Az Azure a "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/" alatt teszi elérhetővé a nevet az API-n<br>resourceProviders/{resourceProviderName}/{endpointDefinitionName}'
routingType | *nem* | Meghatározza a szerződés típusát a **végponttal**. Ha nincs megadva, a rendszer alapértelmezés szerint a "proxy" értéket adja meg.
endpoint | *igen* | A végpont, amelybe a kérelmeket át kell irányítani. Ez kezeli a választ, valamint a kérés bármely mellékhatását.

### <a name="building-custom-resources"></a>Egyéni erőforrások kiépítése

A **ResourceTypes** az Azure-ba felvett új egyéni erőforrásokat írják le. Ezek az alapszintű REST-módszerek teszik lehetővé. [További információ az egyéni erőforrások létrehozásáról](./custom-providers-resources-endpoint-how-to.md)

Példa egyéni erőforrás-szolgáltatóra a **resourceTypes**:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

Az Azure-hoz a fenti mintához hozzáadott API-k:

HttpMethod | Minta URI-ja | Description
---|---|---
PUT | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>szolgáltatók/Microsoft. CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}? API-Version = 2018 – 09 -01 – előzetes verzió | Az Azure REST API hívást indít egy új erőforrás létrehozásához.
DELETE | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>szolgáltatók/Microsoft. CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}? API-Version = 2018 – 09 -01 – előzetes verzió | Az Azure REST API egy meglévő erőforrás törlését hívja meg.
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>szolgáltatók/Microsoft. CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}? API-Version = 2018 – 09 -01 – előzetes verzió | Az Azure REST API egy meglévő erőforrás beolvasására irányuló hívást hív meg.
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>szolgáltatók/Microsoft. CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources? API-Version = 2018-09 -01-előzetes verzió | Az Azure REST API meghívja a meglévő erőforrások listájának beolvasását.

### <a name="building-custom-actions"></a>Egyéni műveletek készítése

A **műveletek** az Azure-ba felvett új műveleteket írják le. Ezek az erőforrás-szolgáltatón vagy egy **resourceType** belül lehetnek elérhetők. [További információ az egyéni műveletek létrehozásáról](./custom-providers-action-endpoint-how-to.md)

Példa egyéni erőforrás-szolgáltatóra a **műveletekkel**:

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

Az Azure-hoz a fenti mintához hozzáadott API-k:

HttpMethod | Minta URI-ja | Description
---|---|---
POST | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>szolgáltatók/Microsoft. CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomAction? API-Version = 2018-09 -01-előzetes verzió | Az Azure REST API hívást indít a művelet aktiválásához.

## <a name="looking-for-help"></a>Segítség keresése

Ha kérdése van az Azure egyéni erőforrás-szolgáltatói fejlesztéséhez, próbálkozzon a [stack Overflowával](https://stackoverflow.com/questions/tagged/azure-custom-providers). Előfordulhat, hogy egy hasonló kérdést már megtettek és megválaszoltak, ezért először A feladás előtt érdemes megnézni. A címke hozzáadásával ```azure-custom-providers``` gyors választ kaphat!

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta az egyéni szolgáltatókat. Az egyéni szolgáltató létrehozásához nyissa meg a következő cikket.

- [Rövid útmutató: Azure egyéni erőforrás-szolgáltató létrehozása és egyéni erőforrások üzembe helyezése](./create-custom-provider.md)
- [Oktatóanyag: egyéni műveletek és erőforrások létrehozása az Azure-ban](./tutorial-get-started-with-custom-providers.md)
- [Útmutató: egyéni műveletek hozzáadása az Azure REST APIhoz](./custom-providers-action-endpoint-how-to.md)
- [Útmutató: egyéni erőforrások hozzáadása az Azure REST APIhoz](./custom-providers-resources-endpoint-how-to.md)

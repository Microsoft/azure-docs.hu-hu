---
title: Erőforrások előkészítése
description: Ismerje meg, hogyan végezhet erőforrás-bevezetést az Azure-beli egyéni szolgáltatók használatával a más Azure-erőforrástípusok felügyeletének és konfigurálásának végrehajtásához.
author: jjbfour
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: jobreen
ms.openlocfilehash: 1846b036f12fe7e691021ec0248782cad946d9b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "75650408"
---
# <a name="azure-custom-providers-resource-onboarding-overview"></a>Az Azure egyéni szolgáltatók erőforrás-előkészítésének áttekintése

Az Azure Custom Providers erőforrás-előkészítés az Azure-erőforrástípusok bővíthető modellje. Lehetővé teszi, hogy a meglévő Azure-erőforrások széles skálán alkalmazza a műveleteket vagy a felügyeletet. További információ: Hogyan bővíthető az Azure-beli [Egyéni szolgáltatók az Azure](overview.md)-ban. Ez a cikk a következőket ismerteti:

- Milyen erőforrás-előkészítést végezhet.
- Erőforrások bevezetésének alapjai és használatuk módja.
- Hol találhatók útmutatók és kód minták az első lépésekhez.

> [!IMPORTANT]
> Az egyéni szolgáltatók jelenleg nyilvános előzetes verzióban érhetők el.
> Ezt az előzetes verziót szolgáltatói szerződés nélkül biztosítjuk, és nem ajánlott éles környezetben üzemelő számítási feladatokhoz. Előfordulhat, hogy bizonyos funkciók nem támogatottak, vagy korlátozva vannak a képességei.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-can-resource-onboarding-do"></a>Mit tehet az erőforrás-előkészítés?

Az [Egyéni Azure-szolgáltatókhoz](./custom-providers-resources-endpoint-how-to.md)hasonlóan az egyéni erőforrások, az erőforrás-előkészítés egy olyan szerződést határoz meg, amely a "bevezetési" kérelmeket egy végpontra fogja kijelölni. Az egyéni erőforrásoktól eltérően az erőforrás-előkészítés nem hoz létre új erőforrástípust. Ehelyett lehetővé teszi a meglévő erőforrástípusok kiterjesztését. Az erőforrás-előkészítés Azure Policy, így az erőforrások kezelése és konfigurálása a skálán végezhető el. Néhány példa az erőforrás-bevezetési munkafolyamatokra:

- Telepítse és kezelje a virtuálisgép-bővítményeket.
- Az alapértelmezett beállítások feltöltése és konfigurálása az Azure Storage-fiókokban.
- Az alapkonfiguráció diagnosztikai beállításainak méretezése.

## <a name="resource-onboarding-basics"></a>Az erőforrás-előkészítés alapjai

A Microsoft. CustomProviders/resourceProviders és a Microsoft. CustomProviders/Associations erőforrástípus használatával konfigurálhatja az erőforrás-előkészítést az Azure egyéni szolgáltatókon keresztül. Ha egy egyéni szolgáltató erőforrás-előkészítését szeretné engedélyezni, a konfigurációs folyamat során hozzon létre egy "társítások" nevű **resourceType** egy "bővítmény" kifejezést tartalmazó **routingType** . A Microsoft. CustomProviders/Associations és a Microsoft. CustomProviders/resourceProviders nem kell ugyanahhoz az erőforráscsoporthoz tartoznia.

Íme egy példa Azure-beli egyéni szolgáltató:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "associations",
        "routingType": "Proxy,Cache,Extension",
        "endpoint": "https://microsoft.com/"
      }
    ]
  },
  "location": "eastus"
}
```

Tulajdonság | Kötelező? | Description
---|---|---
name | Yes | A végpont definíciójának neve. Az erőforrások bevezetéséhez a névnek "társítás" típusúnak kell lennie.
routingType | Yes | Meghatározza a végponthoz tartozó szerződés típusát. Az erőforrások bevezetéséhez az érvényes **routingTypes** a következők: "proxy, cache, Extension" és "webhook, gyorsítótár, bővítmény".
endpoint | Yes | A végpont, amelybe a kérelmeket át kell irányítani. Ezzel a megoldással kezelheti a kérés válaszát és mellékhatásait.

Miután létrehozta az egyéni szolgáltatót a társítások erőforrástípus alapján, célként a Microsoft. CustomProviders/Associations szolgáltatást használhatja. A Microsoft. CustomProviders/Associations egy kiterjesztési erőforrás, amely bármely más Azure-erőforrást kiterjesztheti. A Microsoft. CustomProviders/Associations-példányok létrehozásakor a rendszer a **targetresourceid azonosítója** tulajdonságot használja, amelynek érvényes Microsoft. CustomProviders/ResourceProviders vagy Microsoft. Solutions/Applications erőforrás-azonosítónak kell lennie. Ezekben az esetekben a kérést a rendszer a létrehozott Microsoft. CustomProviders/resourceProviders példányának társítási erőforrástípus számára továbbítja.

> [!NOTE]
> Ha a Microsoft. Solutions/Applications erőforrás-azonosító a **targetresourceid azonosítója** van megadva, akkor a felügyelt erőforráscsoporthoz a "Public" névvel telepített Microsoft. CustomProviders/resourceProviders kell lennie.

Azure-beli egyéni szolgáltatók társításának mintája:

```JSON
{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    ...
  }
}
```

Tulajdonság | Kötelező? | Description
---|---|---
Targetresourceid azonosítója | Yes | A Microsoft. CustomProviders/resourceProviders vagy a Microsoft. Solutions/alkalmazások erőforrás-azonosítója.

## <a name="how-to-use-resource-onboarding"></a>Az erőforrás-előkészítés használata

Az erőforrás-előkészítés úgy működik, hogy más erőforrásokat is kiterjeszt a Microsoft. CustomProviders/társítások bővítmény-erőforrással. A következő példában a rendszer egy virtuális gépre vonatkozó kérelmet küld, de bármely erőforrás kiterjeszthető.

Először létre kell hoznia egy egyéni szolgáltatói erőforrást egy társítás erőforrástípus használatával. Ez deklarálja azt a visszahívási URL-címet, amelyet a rendszer akkor használ, amikor létrejön egy megfelelő Microsoft. CustomProviders/Associations-erőforrás, amely az egyéni szolgáltatót célozza meg.

Minta Microsoft. CustomProviders/resourceProviders Create kérelem:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
  "properties": {
    "resourceTypes": [
      {
        "name": "associations",
        "routingType": "Proxy,Cache,Extension",
        "endpoint": "https://{myCustomEndpoint}/"
      }
    ]
  },
  "location": "{location}"
}
```

Miután létrehozta az egyéni szolgáltatót, megcélozhat más erőforrásokat, és alkalmazhatja az egyéni szolgáltató mellékhatásait is.

Minta Microsoft. CustomProviders/Associations Create kérelem:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/providers/Microsoft.CustomProviders/associations/{associationName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
  }
}
```

Ezt a kérést a rendszer továbbítja a létrehozott egyéni szolgáltató által megadott végpontnak, amelyet a **targetresourceid azonosítója** a következő űrlapon hivatkozik:

``` HTTP
PUT https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/associations/{associationName}
X-MS-CustomProviders-ExtensionPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/providers/Microsoft.CustomProviders/associations/{associationName}
X-MS-CustomProviders-ExtendedResource: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}

{
  "properties": {
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
  }
}
```

A végpontnak egy alkalmazás/JSON `Content-Type` és egy érvényes JSON-válasz törzstel kell válaszolnia. A JSON **Tulajdonságok** objektumában visszaadott mezők a társítás visszatérési válaszához lesznek hozzáadva.

## <a name="getting-help"></a>Segítség kérése

Ha kérdése van az Azure-beli egyéni erőforrás-szolgáltatók fejlesztésével kapcsolatban, próbálja meg megkérdezni őket [stack Overflowon](https://stackoverflow.com/questions/tagged/azure-custom-providers). Egy hasonló kérdés megválaszolása már megtörtént, ezért először A feladás előtt érdemes megnéznie. A címke hozzáadásával ```azure-custom-providers``` gyors választ kaphat!

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta az egyéni szolgáltatókat. További információt a következő cikkekben talál:

- [Oktatóanyag: erőforrás-előkészítés egyéni szolgáltatókkal](./tutorial-resource-onboarding.md)
- [Oktatóanyag: egyéni műveletek és erőforrások létrehozása az Azure-ban](./tutorial-get-started-with-custom-providers.md)
- [Gyors útmutató: egyéni erőforrás-szolgáltató létrehozása és egyéni erőforrások üzembe helyezése](./create-custom-provider.md)
- [Útmutató: egyéni műveletek hozzáadása egy Azure-REST APIhoz](./custom-providers-action-endpoint-how-to.md)
- [Útmutató: egyéni erőforrások hozzáadása Azure-REST APIhoz](./custom-providers-resources-endpoint-how-to.md)

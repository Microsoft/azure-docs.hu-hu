---
title: Egyéni szolgáltató létrehozása és használata
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre és használhat egy egyéni Azure-szolgáltatót. Egyéni szolgáltatók használata az Azure-beli munkafolyamatok módosításához.
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4f425af7681b666b42fbcc70ac0e4c31d9df6d49
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87503752"
---
# <a name="create-and-use-a-custom-provider"></a>Egyéni szolgáltató létrehozása és használata

Az egyéni szolgáltató az Azure és a végpont közötti szerződés. Az egyéni szolgáltatók használatával megváltoztathatja az Azure-beli munkafolyamatokat. Ez az oktatóanyag az egyéni szolgáltató létrehozásának folyamatát mutatja be. Ha nem ismeri az egyéni Azure-szolgáltatókat, tekintse meg [Az Azure-beli egyéni erőforrás-szolgáltatók áttekintését](overview.md).

## <a name="create-a-custom-provider"></a>Egyéni szolgáltató létrehozása

> [!NOTE]
> Ez az oktatóanyag nem mutatja be a végpontok létrehozási módját. Ha nem rendelkezik REST-végponttal, kövesse az [oktatóanyagot a REST-végpontok létrehozásához](./tutorial-custom-providers-function-authoring.md), amely az aktuális oktatóanyag alapja.

A végpont létrehozása után létrehozhat egy egyéni szolgáltatót a szolgáltató és a végpont közötti szerződések létrehozásához. Egyéni szolgáltató esetén megadhatja a végpont-definíciók listáját:

```JSON
{
  "name": "myEndpointDefinition",
  "routingType": "Proxy",
  "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
}
```

Tulajdonság | Kötelező | Leírás
---|---|---
**név** | Yes | A végpont definíciójának neve. Az Azure a/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders alatti API-n keresztül teszi elérhetővé a nevet<br>/resourceProviders/{resourceProviderName}/{endpointDefinitionName}
**routingType** | No | A végponti szerződés típusa Ha az érték nincs megadva, a rendszer alapértelmezés szerint a "proxy" értéket adja meg.
**végpont** | Yes | A végpont, amelybe a kérelmeket át kell irányítani. Ez a végpont kezeli a kérés válaszát, illetve a kérelem mellékhatásait.

A **végpont** értéke az Azure Function alkalmazás triggerének URL-címe. A `<yourapp>` , `<funcname>` , és `<functionkey>` helyőrzőket a létrehozott Function alkalmazás értékeivel kell helyettesíteni.

## <a name="define-custom-actions-and-resources"></a>Egyéni műveletek és erőforrások definiálása

Az egyéni szolgáltató a **műveletek** és a **resourceTypes** tulajdonságai között modellezett végpont-definíciók listáját tartalmazza. A **műveletek** tulajdonság az egyéni szolgáltató által elérhetővé tett egyéni műveletekre mutat, a **resourceTypes** tulajdonság pedig az egyéni erőforrások. Ebben az oktatóanyagban az egyéni szolgáltatónak van egy nevű **művelet** tulajdonsága `myCustomAction` és egy nevű **resourceTypes** tulajdonsága `myCustomResources` .

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
      }
    ],
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
      }
    ]
  },
  "location": "eastus"
}
```

## <a name="deploy-the-custom-provider"></a>Az egyéni szolgáltató üzembe helyezése

> [!NOTE]
> A **végpontok** értékeit az előző oktatóanyagban létrehozott Function alkalmazás trigger URL-címével kell helyettesíteni.

Az előző egyéni szolgáltatót Azure Resource Manager sablonnal is telepítheti:

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders",
            "name": "myCustomProvider",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "actions": [
                    {
                        "name": "myCustomAction",
                        "routingType": "Proxy",
                        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
                    }
                ],
                "resourceTypes": [
                    {
                        "name": "myCustomResources",
                        "routingType": "Proxy",
                        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
                    }
                ]
            }
        }
    ]
}
```

## <a name="use-custom-actions-and-resources"></a>Egyéni műveletek és erőforrások használata

Miután létrehozta az egyéni szolgáltatót, használhatja az új Azure API-kat. A következő lapok bemutatják, hogyan hívhat meg és használhat egyéni szolgáltatót.

### <a name="custom-actions"></a>Egyéni műveletek

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!NOTE]
> A és a helyőrzőket le kell cserélnie `{subscriptionId}` `{resourceGroupName}` az előfizetés és az erőforráscsoport között, ahol az egyéni szolgáltatót telepítette.

```azurecli-interactive
az resource invoke-action --action myCustomAction \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider \
                          --request-body
                            '{
                                "hello": "world"
                            }'
```

Paraméter | Kötelező | Leírás
---|---|---
*művelet* | Yes | Az egyéni szolgáltatóban definiált művelet neve
*azonosítók* | Yes | Az egyéni szolgáltató erőforrás-azonosítója
*kérelem – törzs* | No | A végpontnak küldendő kérelem törzse

# <a name="template"></a>[Sablon](#tab/template)

Nincsenek.

---

### <a name="custom-resources"></a>Egyéni erőforrások

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!NOTE]
> A és a helyőrzőket le kell cserélnie `{subscriptionId}` `{resourceGroupName}` az előfizetés és az erőforráscsoport között, ahol az egyéni szolgáltatót telepítette.

#### <a name="create-a-custom-resource"></a>Egyéni erőforrás létrehozása

```azurecli-interactive
az resource create --is-full-object \
                   --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1 \
                   --properties
                    '{
                        "location": "eastus",
                        "properties": {
                            "hello" : "world"
                        }
                    }'
```

Paraméter | Kötelező | Leírás
---|---|---
*teljes objektum* | Yes | Azt jelzi, hogy a tulajdonságok objektum tartalmaz-e más beállításokat, például a helyet, a címkéket, az SKU-t vagy a csomagot.
*id* | Yes | Az egyéni erőforrás erőforrás-azonosítója. Ez az azonosító az egyéni szolgáltató erőforrás-AZONOSÍTÓjának kiterjesztése.
*Tulajdonságok* | Yes | A végpontnak küldendő kérelem törzse.

#### <a name="delete-a-custom-resource"></a>Egyéni erőforrás törlése

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Paraméter | Kötelező | Leírás
---|---|---
*id* | Yes | Az egyéni erőforrás erőforrás-azonosítója. Ez az azonosító az egyéni szolgáltató erőforrás-AZONOSÍTÓjának kiterjesztése.

#### <a name="retrieve-a-custom-resource"></a>Egyéni erőforrás lekérése

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Paraméter | Kötelező | Leírás
---|---|---
*id* | Yes | Az egyéni erőforrás erőforrás-azonosítója. Ez az azonosító az egyéni szolgáltató erőforrás-AZONOSÍTÓjának kiterjesztése.

# <a name="template"></a>[Sablon](#tab/template)

Példa Resource Manager-sablonra:

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "name": "myCustomProvider/myTestResourceName1",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "hello": "world"
            }
        }
    ]
}
```

Paraméter | Kötelező | Leírás
---|---|---
*resourceTypeName* | Yes | Az `name` Egyéni szolgáltatóban definiált **resourceTypes** tulajdonság értéke.
*resourceProviderName* | Yes | Az egyéni szolgáltató példányának neve.
*customResourceName* | Yes | Az egyéni erőforrás neve.

---

> [!NOTE]
> Az egyéni szolgáltató üzembe helyezésének és használatának befejezése után ne felejtse el törölni az összes létrehozott erőforrást, beleértve az Azure Function alkalmazást is.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta az egyéni szolgáltatókat. További információkért lásd:

- [Útmutató: egyéni műveletek hozzáadása az Azure REST APIhoz](./custom-providers-action-endpoint-how-to.md)
- [Útmutató: egyéni erőforrások hozzáadása az Azure REST APIhoz](./custom-providers-resources-endpoint-how-to.md)

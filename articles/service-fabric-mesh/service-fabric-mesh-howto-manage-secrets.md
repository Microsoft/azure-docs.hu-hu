---
title: Az Azure Service Fabric Mesh alkalmazás titkainak kezelése
description: Az alkalmazási titkokat felügyelheti, így biztonságosan hozhat létre és helyezhet üzembe egy Service Fabric Mesh-alkalmazást.
ms.date: 4/2/2019
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.openlocfilehash: b3be0c2b21c3405f4f42b2ff4d02ca95c78956de
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2021
ms.locfileid: "99626960"
---
# <a name="manage-service-fabric-mesh-application-secrets"></a>Service Fabric Mesh-alkalmazás titkainak kezelése

> [!IMPORTANT]
> Az Azure Service Fabric Mesh előzetes verziója ki lett vonva. Az új központi telepítések többé nem lesznek engedélyezve a Service Fabric Mesh API-n keresztül. A meglévő központi telepítések támogatása a 2021. április 28. után folytatódik.
> 
> Részletekért lásd: az [Azure Service Fabric Mesh előzetes verziójának nyugdíjazása](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Service Fabric Mesh a titkokat Azure-erőforrásokként támogatja. A Service Fabric Mesh titkos kulcsa lehet bármilyen bizalmas szöveges információ, például a tárolási kapcsolatok sztringje, jelszava vagy más olyan érték, amelyet biztonságosan kell tárolni és továbbítani. Ez a cikk bemutatja, hogyan helyezheti üzembe és kezelheti a titkokat a Service Fabric Biztonságos tár szolgáltatás használatával.

A háló alkalmazás titkos kulcsa a következőkből áll:
* A **titkok** erőforrása, amely egy olyan tároló, amely a szöveges titkokat tárolja. A **titkok** erőforrásban található titkos kódok tárolása és továbbítása biztonságos.
* Egy vagy több **titok/érték** erőforrás, amely a **titkok** erőforrás-tárolóban van tárolva. Az egyes **titkok/értékek** erőforrásait a verziószáma különbözteti meg. A **Secret/Values** erőforrás verziószáma nem módosítható, csak új verzió hozzáfűzése.

A titkok kezelése a következő lépésekből áll:
1. A Mesh **Secrets** -erőforrás deklarálása egy Azure Resource Model-YAML vagy JSON-fájlban a inlinedValue-típus és a SecretsStoreRef ContentType-definíciók használatával.
2. A **Secrets** **/Values** típusú erőforrások deklarálása egy Azure Resource Model-YAML vagy JSON-fájlban, amelyet a titkok erőforrása tárol (az 1. lépésből).
3. Mesh-alkalmazás módosítása a rácsvonalak titkos értékeire való hivatkozáshoz.
4. A Mesh alkalmazás üzembe helyezése vagy működés közbeni frissítése a titkos értékek felhasználása érdekében.
5. Használja az Azure "az" CLI-parancsait Biztonságos tár szolgáltatás életciklus-felügyelethez.

## <a name="declare-a-mesh-secrets-resource"></a>A Mesh Secrets erőforrás deklarálása
A Mesh Secrets erőforrást egy Azure-beli erőforrás-modell JSON-vagy YAML-fájljában deklaráljuk a inlinedValue típus definíciójának használatával. A Mesh Secrets erőforrás a Biztonságos tár szolgáltatás forrásból származó titkokat támogatja. 
>
Az alábbi példa bemutatja, hogyan deklarálhatja a Mesh Secrets-erőforrásokat egy JSON-fájlban:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "WestUS",
      "metadata": {
        "description": "Location of the resources (e.g. westus, eastus, westeurope)."
      }
    }
  },
  "sfbpHttpsCertificate": {
      "type": "string",
      "metadata": {
        "description": "Plain Text Secret Value that your container ingest"
      }
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
      "name": "sfbpHttpsCertificate.pfx",
      "type": "Microsoft.ServiceFabricMesh/secrets",
      "location": "[parameters('location')]", 
      "dependsOn": [],
      "properties": {
        "kind": "inlinedValue",
        "description": "SFBP Application Secret",
        "contentType": "text/plain",
      }
    }
  ]
}
```
Az alábbi példa bemutatja, hogyan deklarálhatja a Mesh Secrets-erőforrásokat egy YAML-fájlban:
```yaml
    services:
      - name: helloWorldService
        properties:
          description: Hello world service.
          osType: linux
          codePackages:
            - name: helloworld
              image: myapp:1.0-alpine
              resources:
                requests:
                  cpu: 2
                  memoryInGB: 2
              endpoints:
                - name: helloWorldEndpoint
                  port: 8080
          secrets:
            - name: MySecret.txt
            description: My Mesh Application Secret
            secret_type: inlinedValue
            content_type: SecretStoreRef
            value: mysecret
    replicaCount: 3
    networkRefs:
      - name: mynetwork
```

## <a name="declare-mesh-secretsvalues-resources"></a>A Mesh Secrets/Values típusú erőforrások deklarálása
A Mesh Secrets/Values erőforrásai az előző lépésben meghatározott rácsvonal-titkok erőforrásaitól függenek.

A "Resources" (erőforrások) szakasz "Value:" és "Name:" mezők közötti kapcsolata esetén: a "Name:" karakterlánc második része, amely egy kettősponttal van elválasztva, a titkos kulcshoz használt verziószám, a kettőspontnak pedig meg kell egyeznie annak a szembőség-értéknek a nevével, amelyhez függőséggel rendelkezik. Az elem esetében például ```name: mysecret:1.0``` a verziószám 1,0, a névnek pedig ```mysecret``` meg kell egyeznie a korábban definiált értékkel ```"value": "mysecret"``` .

>
Az alábbi példa bemutatja, hogyan deklarálhatja a Mesh Secrets/Values-erőforrásokat egy JSON-fájlban:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "WestUS",
      "metadata": {
        "description": "Location of the resources (e.g. westus, eastus, westeurope)."
      }
    }
  },
  "sfbpHttpsCertificate": {
      "type": "string",
      "metadata": {
        "description": "Plain Text Secret Value that your container ingest"
      }
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
      "name": "sfbpHttpsCertificate.pfx",
      "type": "Microsoft.ServiceFabricMesh/secrets",
      "location": "[parameters('location')]", 
      "dependsOn": [],
      "properties": {
        "kind": "inlinedValue",
        "description": "SFBP Application Secret",
        "contentType": "text/plain",
      }
    },
    {
      "apiVersion": "2018-07-01-preview",
      "name": "sfbpHttpsCertificate.pfx/2019.02.28",
      "type": "Microsoft.ServiceFabricMesh/secrets/values",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/secrets/sfbpHttpsCertificate.pfx"
      ],
      "properties": {
        "value": "[parameters('sfbpHttpsCertificate')]"
      }
    }
  ],
}
```
Az alábbi példa bemutatja, hogyan deklarálhatja a Mesh Secrets/Values erőforrásait egy YAML-fájlban:
```yaml
    services:
      - name: helloWorldService
        properties:
          description: Hello world service.
          osType: linux
          codePackages:
            - name: helloworld
              image: myapp:1.0-alpine
              resources:
                requests:
                  cpu: 2
                  memoryInGB: 2
              endpoints:
                - name: helloWorldEndpoint
                  port: 8080
          Secrets:
            - name: MySecret.txt
            description: My Mesh Application Secret
            secret_type: inlinedValue
            content_type: SecretStoreRef
            value: mysecret
            - name: mysecret:1.0
            description: My Mesh Application Secret Value
            secret_type: value
            content_type: text/plain
            value: "P@ssw0rd#1234"
    replicaCount: 3
    networkRefs:
      - name: mynetwork
```

## <a name="modify-mesh-application-to-reference-mesh-secret-values"></a>Rácsvonal-alkalmazás módosítása a rácsvonalak titkos értékeinek hivatkozásához
Service Fabric Mesh-alkalmazásoknak a következő két karakterláncot kell figyelembe venniük Biztonságos tár szolgáltatás titkos értékek felhasználása érdekében:
1. A Microsoft. ServiceFabricMesh/Secrets. name a fájl nevét tartalmazza, és a Secrets értéket fogja tartalmazni szöveges formátumban.
2. A (z) "Fabric_SettingPath" Windows vagy Linux környezeti változó tartalmazza annak a könyvtárnak az elérési útját, ahol a Biztonságos tár szolgáltatás titkok értékeit tartalmazó fájlok elérhetők lesznek. Ez a "C:\Settings" a Windows által üzemeltetett és "/var/Settings" a Linux által üzemeltetett Mesh-alkalmazásokhoz.

## <a name="deploy-or-use-a-rolling-upgrade-for-mesh-application-to-consume-secret-values"></a>A Mesh-alkalmazás működés közbeni frissítésének üzembe helyezése vagy használata a titkos értékek felhasználása érdekében
A titkos kulcsok és/vagy a verzióval ellátott titkok/értékek létrehozása az erőforrás-modell által deklarált központi telepítésekre korlátozódik. Ezeknek az erőforrásoknak a létrehozásához az alábbi módon kell átadnia egy erőforrás-modell JSON-vagy YAML-fájlját az az **Mesh Deployment** parancs használatával:

```azurecli-interactive
az mesh deployment create –-<template-file> or --<template-uri>
```

## <a name="azure-cli-commands-for-secure-store-service-lifecycle-management"></a>Azure CLI-parancsok Biztonságos tár szolgáltatás életciklus-felügyelethez

### <a name="create-a-new-secrets-resource"></a>Új titkok erőforrásának létrehozása
```azurecli-interactive
az mesh deployment create –-<template-file> or --<template-uri>
```
Adja át a **template-file** vagy a **template-URI elemet** (de ne mindkettőt).

Például:
- az Mesh Deployment Create --c:\MyMeshTemplates\SecretTemplate1.txt
- az Mesh Deployment Create--https: \/ /www.fabrikam.com/MyMeshTemplates/SecretTemplate1.txt

### <a name="show-a-secret"></a>Titkos kód megjelenítése
A titok leírását adja vissza (de nem az értéket).
```azurecli-interactive
az mesh secret show --Resource-group <myResourceGroup> --secret-name <mySecret>
```

### <a name="delete-a-secret"></a>Titkos kód törlése

- A titkos kód nem törölhető, amíg egy háló alkalmazás hivatkozik rá.
- A titkok erőforrásának törlése törli az összes titkot/erőforrás-verziót.
  ```azurecli-interactive
  az mesh secret delete --Resource-group <myResourceGroup> --secret-name <mySecret>
  ```

### <a name="list-secrets-in-subscription"></a>Az előfizetéshez tartozó titkos kódok listázása
```azurecli-interactive
az mesh secret list
```
### <a name="list-secrets-in-resource-group"></a>Az erőforráscsoport titkainak listázása
```azurecli-interactive
az mesh secret list -g <myResourceGroup>
```
### <a name="list-all-versions-of-a-secret"></a>A titkos kulcs összes verziójának listázása
```azurecli-interactive
az mesh secretvalue list --Resource-group <myResourceGroup> --secret-name <mySecret>
```

### <a name="show-secret-version-value"></a>Titkos verzió értékének megjelenítése
```azurecli-interactive
az mesh secretvalue show --Resource-group <myResourceGroup> --secret-name <mySecret> --version <N>
```

### <a name="delete-secret-version-value"></a>Titkos verzió értékének törlése
```azurecli-interactive
az mesh secretvalue delete --Resource-group <myResourceGroup> --secret-name <mySecret> --version <N>
```

## <a name="next-steps"></a>Következő lépések 
Ha többet szeretne megtudni a Service Fabric Meshról, olvassa el az áttekintést:
- [Service Fabric Mesh – áttekintés](service-fabric-mesh-overview.md)

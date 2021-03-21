---
title: Zóna – redundáns beállításjegyzék a magas rendelkezésre állás érdekében
description: További információ a zónák redundanciának engedélyezéséről a Azure Container Registry. Hozzon létre egy tároló-beállításjegyzéket vagy replikációt egy Azure rendelkezésre állási zónában. A Zone redundancia a prémium szintű szolgáltatási szint egyik funkciója.
ms.topic: article
ms.date: 02/23/2021
ms.custom: references_regions
ms.openlocfilehash: a190ea68f41196fb11c20259b9953f516d6f5370
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102203861"
---
# <a name="enable-zone-redundancy-in-azure-container-registry-for-resiliency-and-high-availability"></a>A zónák redundanciának engedélyezése Azure Container Registry a rugalmasság és a magas rendelkezésre állás érdekében

A [geo-replikáció](container-registry-geo-replication.md)mellett, amely egy vagy több Azure-régióban replikálja a regisztrációs adatait, így biztosítva a rendelkezésre állást és csökkenti a regionális műveletek késését, Azure Container Registry támogatja a nem kötelező *zóna-redundanciát*. A [zóna-redundancia](../availability-zones/az-overview.md#availability-zones) biztosítja a rugalmasságot és a magas rendelkezésre állást egy adott régióban lévő beállításjegyzék vagy replikációs erőforrás (replika) számára.

Ez a cikk bemutatja, hogyan állíthat be egy zóna-redundáns tároló-beállításjegyzéket vagy-replikát az Azure CLI, Azure Portal vagy Azure Resource Manager sablon használatával. 

A Zone redundancia a prémium szintű tároló beállításjegyzék-szolgáltatási szintjének **előzetes** verziójú funkciója. További információ a beállításjegyzék szolgáltatási szintjeiről és korlátairól: [Azure Container Registry szolgáltatási szintek](container-registry-skus.md).

## <a name="preview-limitations"></a>Előzetes verzió korlátozásai

* Jelenleg a következő régiókban támogatott: USA keleti régiója, USA 2. keleti régiója, USA 2. nyugati régiója, Észak-Európa, Nyugat-Európa, Kelet-Japán.
* A régió-átalakítások rendelkezésre állási zónákra jelenleg nem támogatottak. Ha engedélyezni szeretné a rendelkezésre állási zónák támogatását egy adott régióban, a beállításjegyzéket a kívánt régióban kell létrehoznia, a rendelkezésre állási zóna támogatásával, vagy a rendelkezésre állási zóna támogatását engedélyező replikált régiót kell hozzáadnia.
* A zóna redundancia nem tiltható le egy régióban.
* Az [ACR-feladatok](container-registry-tasks-overview.md) még nem támogatják a rendelkezésre állási zónákat.

## <a name="about-zone-redundancy"></a>A zóna redundancia

Az Azure-beli [rendelkezésre állási zónák](../availability-zones/az-overview.md) használatával rugalmas és magas rendelkezésre állású Azure Container Registry hozható létre egy Azure-régión belül. Például a szervezetek beállíthat egy zóna-redundáns Azure Container registryt más [támogatott Azure-erőforrásokkal](../availability-zones/az-region.md) , hogy megfeleljenek az adattárolási vagy egyéb megfelelőségi követelményeknek, miközben magas rendelkezésre állást biztosítanak a régión belül.

A Azure Container Registry támogatja a [geo-replikálást](container-registry-geo-replication.md)is, amely több régióban replikálja a szolgáltatást, így a redundancia és a helység kiszámításával más helyekre is kiszámíthatja az erőforrásokat. Egy régión belüli redundancia rendelkezésre állási zónáinak és a több régióban történő földrajzi replikálásnak a kombinációja fokozza a beállításjegyzék megbízhatóságát és teljesítményét.

A rendelkezésre állási zónák egy Azure-régióban található egyedi fizikai helyek. A rugalmasság biztosítása érdekében minden engedélyezett régióban legalább három elkülönített zóna található. Minden zónához egy vagy több független energiaellátással, hűtéssel és hálózatkezeléssel felszerelt adatközpont tartozik. A zóna-redundancia beállításakor a beállításjegyzék (vagy egy másik régió beállításjegyzék-replikája) replikálódik a régió összes rendelkezésre állási zónájában, hogy az adatközpont meghibásodása esetén is elérhető maradjon.

## <a name="create-a-zone-redundant-registry---cli"></a>Zóna létrehozása – redundáns beállításjegyzék – parancssori felület

Ha az Azure CLI-t szeretné használni a zóna redundancia engedélyezéséhez, szüksége lesz az Azure CLI 2.17.0 vagy újabb verziójára, vagy Azure Cloud Shellra. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Szükség esetén futtassa az az [Group Create](/cli/azure/group#az_group_create) parancsot a beállításjegyzékhez tartozó erőforráscsoport létrehozásához.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-zone-enabled-registry"></a>Zóna használatára képes beállításjegyzék létrehozása

Az az [ACR Create](/cli/azure/acr#az_acr_create) paranccsal hozzon létre egy zóna-redundáns beállításjegyzéket a prémium szintű szolgáltatási szinten. Válasszon egy olyan régiót, amely [támogatja a rendelkezésre állási zónákat](../availability-zones/az-region.md) Azure Container Registry számára. A következő példában a zóna redundancia engedélyezve van a *eastus* régióban. `az acr create`További beállításjegyzék-beállításokért tekintse meg a parancs súgóját.

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --location eastus \
  --zone-redundancy enabled \
  --sku Premium
```

A parancs kimenetében jegyezze fel a `zoneRedundancy` beállításjegyzék tulajdonságát. Ha engedélyezve van, a beállításjegyzék a zóna redundáns:

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

### <a name="create-zone-redundant-replication"></a>Zóna – redundáns replikáció létrehozása

Futtassa az az [ACR Replication Create](/cli/azure/acr/replication#az_acr_replication_create) parancsot, és hozzon létre egy zóna-redundáns beállításjegyzék-replikát egy olyan régióban, amely [támogatja a rendelkezésre állási zónákat](../availability-zones/az-region.md) Azure Container Registry, például *westus2*. 

```azurecli
az acr replication create \
  --location westus2 \
  --resource-group <resource-group-name> \
  --registry <container-registry-name> \
  --zone-redundancy enabled
```
 
A parancs kimenetében jegyezze fel a `zoneRedundancy` replika tulajdonságát. Ha engedélyezve van, a replika a zóna redundáns:

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

## <a name="create-a-zone-redundant-registry---portal"></a>Zóna létrehozása – redundáns beállításjegyzék – portál

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.
1. Válassza **az erőforrás létrehozása**  >  **tárolók**  >  **Container Registry** elemet.
1. Az **alapvető beállítások** lapon válasszon ki vagy hozzon létre egy erőforráscsoportot, és adjon meg egy egyedi beállításjegyzék-nevet. 
1. A **hely** mezőben válasszon ki egy olyan régiót, amely támogatja a zóna-redundancia Azure Container Registry, például az *USA keleti* régióját.
1. Az **SKU** területen válassza a **prémium** lehetőséget.
1. A **rendelkezésre állási zónák** területen válassza az **engedélyezve** lehetőséget.
1. Szükség esetén konfiguráljon további beállításjegyzék-beállításokat, majd válassza a **felülvizsgálat + létrehozás** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget a beállításjegyzék-példány telepítéséhez.

    :::image type="content" source="media/zone-redundancy/enable-availability-zones-portal.png" alt-text="Zóna-redundancia engedélyezése Azure Portal":::

Zóna redundáns replikációjának létrehozása:

1. Navigáljon a prémium szintű tároló beállításjegyzékéhez, és válassza a **replikációk** lehetőséget.
1. A megjelenő térképen válasszon ki egy zöld hatszöget egy olyan régióban, amely támogatja a zóna-redundancia Azure Container Registry, például az **USA 2. nyugati** régióját. Vagy válassza a **+ Hozzáadás** lehetőséget.
1. A **replikáció létrehozása** ablakban erősítse meg a **helyet**. A **rendelkezésre állási zónák** területen válassza az **engedélyezve** lehetőséget, majd válassza a **Létrehozás** lehetőséget.

    :::image type="content" source="media/zone-redundancy/enable-availability-zones-replication-portal.png" alt-text="A zónán belüli redundáns replikálás engedélyezése Azure Portal":::

## <a name="create-a-zone-redundant-registry---template"></a>Zóna létrehozása – redundáns beállításjegyzék – sablon

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Szükség esetén az az [Group Create](/cli/azure/group#az_group_create) paranccsal hozzon létre egy erőforráscsoportot a beállításjegyzékhez egy olyan régióban, amely [támogatja a rendelkezésre állási zónákat](../availability-zones/az-region.md) Azure Container Registryhoz, például *eastus*. Ezt a régiót használja a sablon a beállításjegyzék helyének beállításához.

```azurecli
az group create --name <resource-group-name> --location eastus
```

### <a name="deploy-the-template"></a>A sablon üzembe helyezése 

A következő Resource Manager-sablonnal létrehozható egy zóna – redundáns, földrajzilag replikált beállításjegyzék. A sablon alapértelmezés szerint lehetővé teszi a zóna redundanciát a beállításjegyzékben és egy regionális replikában. 

Másolja az alábbi tartalmat egy új fájlba, és mentse a fájlt egy olyan fájlnév használatával, mint például `registryZone.json` .

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "acrName": {
        "type": "string",
        "defaultValue": "[concat('acr', uniqueString(resourceGroup().id))]",
        "minLength": 5,
        "maxLength": 50,
        "metadata": {
          "description": "Globally unique name of your Azure Container Registry"
        }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Location for registry home replica."
        }
      },
      "acrSku": {
        "type": "string",
        "defaultValue": "Premium",
        "allowedValues": [
          "Premium"
        ],
        "metadata": {
          "description": "Tier of your Azure Container Registry. Geo-replication and zone redundancy require Premium SKU."
        }
      },
      "acrZoneRedundancy": {
        "type": "string",
        "defaultValue": "Enabled",
        "metadata": {
          "description": "Enable zone redundancy of registry's home replica. Requires registry location to support availability zones."
        }
      },
      "acrReplicaLocation": {
        "type": "string",
        "metadata": {
          "description": "Short name for registry replica location."
        }
      },
      "acrReplicaZoneRedundancy": {
        "type": "string",
        "defaultValue": "Enabled",
        "metadata": {
          "description": "Enable zone redundancy of registry replica. Requires replica location to support availability zones."
        }
      }
    },
    "resources": [
      {
        "comments": "Container registry for storing docker images",
        "type": "Microsoft.ContainerRegistry/registries",
        "apiVersion": "2020-11-01-preview",
        "name": "[parameters('acrName')]",
        "location": "[parameters('location')]",
        "sku": {
          "name": "[parameters('acrSku')]",
          "tier": "[parameters('acrSku')]"
        },
        "tags": {
          "displayName": "Container Registry",
          "container.registry": "[parameters('acrName')]"
        },
        "properties": {
          "adminUserEnabled": "[parameters('acrAdminUserEnabled')]",
          "zoneRedundancy": "[parameters('acrZoneRedundancy')]"
        }
      },
      {
        "type": "Microsoft.ContainerRegistry/registries/replications",
        "apiVersion": "2020-11-01-preview",
        "name": "[concat(parameters('acrName'), '/', parameters('acrReplicaLocation'))]",
        "location": "[parameters('acrReplicaLocation')]",
          "dependsOn": [
          "[resourceId('Microsoft.ContainerRegistry/registries/', parameters('acrName'))]"
        ],
        "properties": {
          "zoneRedundancy": "[parameters('acrReplicaZoneRedundancy')]"
        }
      }
    ],
    "outputs": {
      "acrLoginServer": {
        "value": "[reference(resourceId('Microsoft.ContainerRegistry/registries',parameters('acrName')),'2019-12-01-preview').loginServer]",
        "type": "string"
      }
    }
  }
```

Az előző sablonfájl használatával hozza létre a beállításjegyzéket a következő az [Deployment Group Create](/cli/azure/group/deployment#az_group_deployment_create) parancs futtatásával. Ha meg van jelölve, adja meg a következőket:

* egyedi beállításjegyzék-név, vagy paraméterek nélkül helyezheti üzembe a sablont, és egyedi nevet fog létrehozni
* a rendelkezésre állási zónákat támogató replika (például *westus2* ) helye

```azurecli
az deployment group create \
  --resource-group <resource-group-name> \
  --template-file registryZone.json \
  --parameters acrName=<registry-name> acrReplicaLocation=<replica-location>
```

A parancs kimenetében jegyezze fel a `zoneRedundancy` beállításjegyzék és a replika tulajdonságát. Ha engedélyezve van, minden erőforrás a zóna redundáns:

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

## <a name="next-steps"></a>Következő lépések

* További információ a [rendelkezésre állási zónákat támogató régiókról](../availability-zones/az-region.md).
* További információ a [megbízhatóság](/azure/architecture/framework/resiliency/overview) kiépítése az Azure-ban.

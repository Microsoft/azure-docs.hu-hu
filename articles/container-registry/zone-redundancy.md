---
title: Zónaredundáns beállításjegyzék a magas rendelkezésre álláshoz
description: Tudnivalók a zónaredundania engedélyezéséről a Azure Container Registry. Tároló-beállításjegyzéket vagy replikációt hozhat létre egy Azure rendelkezésre állási zónában. A zónaredundania a Prémium szolgáltatási szint egyik funkciója.
ms.topic: article
ms.date: 02/23/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 8c1ab42aa505448bd81ff42eba54727b24773c60
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479015"
---
# <a name="enable-zone-redundancy-in-azure-container-registry-for-resiliency-and-high-availability"></a>Zónaredundania engedélyezése Azure Container Registry rugalmasság és magas rendelkezésre állás érdekében

A georeplikáción kívül, amely egy vagy több [Azure-régióban](container-registry-geo-replication.md)replikálja a beállításjegyzék-adatokat a rendelkezésre állás és a regionális műveletek késésének csökkentése érdekében, a Azure Container Registry támogatja a választható zónaredundaniát.  [A zónaredundania](../availability-zones/az-overview.md#availability-zones) rugalmasságot és magas rendelkezésre állást biztosít egy adott régióban található beállításjegyzék vagy replikációs erőforrás (replika) számára.

Ez a cikk bemutatja, hogyan állíthat be zónaredundáns tárolójegyzéket vagy -replikát az Azure CLI, Azure Portal vagy Azure Resource Manager használatával. 

A zónaredundania **a** Prémium tároló-beállításjegyzék szolgáltatásszint előzetes verziója. További információ a beállításjegyzék szolgáltatási rétegeiről és korlátairól: Azure Container Registry [szolgáltatási szintek.](container-registry-skus.md)

## <a name="preview-limitations"></a>Az előzetes verzió korlátozásai

* Jelenleg a következő régiókban támogatott: USA keleti régiója, USA 2. keleti régiója, USA 2. nyugati régiója, Észak-Európa, Nyugat-Európa, Kelet-Japán.
* A rendelkezésre állási zónákra való régióátalakítások jelenleg nem támogatottak. Ha engedélyezni szeretné a rendelkezésre állási zónák támogatását egy régióban, a regisztrációs adatbázist a kívánt régióban kell létrehozni, és engedélyezni kell a rendelkezésre állási zónák támogatását, vagy egy replikált régiót kell hozzáadni, ha engedélyezve van a rendelkezésre állási zóna támogatása.
* A zónaredundania nem tiltható le egy régióban.
* [ACR-feladatok](container-registry-tasks-overview.md) még nem támogatja a rendelkezésre állási zónákat.

## <a name="about-zone-redundancy"></a>A zónaredundania

Az Azure [rendelkezésre állási zónák használatával](../availability-zones/az-overview.md) rugalmas és magas rendelkezésre állású Azure Container Registryt hozhat létre egy Azure-régióban. A szervezetek például létrehozhatnak egy zónaredundáns [](../availability-zones/az-region.md) Azure Container Registryt más támogatott Azure-erőforrásokkal, hogy eleget tudjanak az adatoknak a tárolóban való hellyel vagy más megfelelőségi követelményekkel, miközben magas rendelkezésre állást tudnak rendelkezésre bocsátani egy adott régióban.

Azure Container Registry támogatja a [](container-registry-geo-replication.md)georeplikációt is, amely több régióban replikálja a szolgáltatást, így lehetővé teszi a redundanciát és a más helyeken lévő számítási erőforrások helyi rendelkezésreságát. A régión belüli redundancia rendelkezésre állási zónái és a több régióban történő georeplikáció együttesen növeli a regisztrációs adatbázis megbízhatóságát és teljesítményét.

A rendelkezésre állási zónák egyedi fizikai helyek egy Azure-régión belül. A rugalmasság biztosítása érdekében minden engedélyezett régióban legalább három elkülönített zóna található. Minden zóna egy vagy több, független áramellátással, hűtéssel és hálózattal ellátott adatközponttal rendelkezik. Ha zónaredundansra van konfigurálva, a rendszer egy beállításjegyzéket (vagy egy másik régióban lévő beállításjegyzék-replikát) replikál a régió összes rendelkezésre állási zónájában, így az adatközpontok meghibásodása esetén is elérhető marad.

## <a name="create-a-zone-redundant-registry---cli"></a>Zónaredundáns beállításjegyzék létrehozása – CLI

Ha az Azure CLI-t szeretné használni a zónaredundania engedélyezéséhez, az Azure CLI 2.17.0-s vagy újabb verziójára, vagy Azure Cloud Shell. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Szükség esetén az [az group create paranccsal](/cli/azure/group#az_group_create) hozzon létre egy erőforráscsoportot a beállításjegyzékhez.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-zone-enabled-registry"></a>Zóna kompatibilis beállításjegyzék létrehozása

Futtassa [az az acr create parancsot](/cli/azure/acr#az_acr_create) egy zónaredundáns beállításjegyzék létrehozásához a Prémium szolgáltatási szinten. Válasszon ki egy olyan régiót, amely támogatja a rendelkezésre [állási zónákat](../availability-zones/az-region.md) a Azure Container Registry. A következő példában a zónaredundania engedélyezve van az *eastus* régióban. További `az acr create` beállításjegyzék-beállításokért tekintse meg a parancs súgóját.

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --location eastus \
  --zone-redundancy enabled \
  --sku Premium
```

A parancs kimenetében jegyezze fel `zoneRedundancy` a beállításjegyzék tulajdonságát. Ha engedélyezve van, a beállításjegyzék zónaredundáns:

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

### <a name="create-zone-redundant-replication"></a>Zónaredundáns replikáció létrehozása

Futtassa [az az acr replication create](/cli/azure/acr/replication#az_acr_replication_create) parancsot egy zónaredundáns beállításjegyzék-replika létrehozásához egy olyan régióban, amely támogatja a rendelkezésre állási zónákat a Azure Container Registry, például a *westus2 régióban.* [](../availability-zones/az-region.md) 

```azurecli
az acr replication create \
  --location westus2 \
  --resource-group <resource-group-name> \
  --registry <container-registry-name> \
  --zone-redundancy enabled
```
 
A parancs kimenetében jegyezze fel `zoneRedundancy` a replika tulajdonságát. Ha engedélyezve van, a replika zónaredundáns:

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

## <a name="create-a-zone-redundant-registry---portal"></a>Zónaredundáns beállításjegyzék létrehozása – portál

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.
1. Válassza **az Erőforrás létrehozása**  >  **Tárolók Container Registry.**  >  
1. Az Alapvető **beállítások lapon** válasszon ki vagy hozzon létre egy erőforráscsoportot, és adjon meg egy egyedi regisztrációs adatbázisnevet. 
1. A **Hely mezőben** válasszon ki egy régiót, amely támogatja a zónaredundaniát a Azure Container Registry számára, például az *USA keleti régiója.*
1. Az **SKU (Termékváltozat)** mezőben válassza a **Premium lehetőséget.**
1. A Rendelkezésre **állási zónákban válassza** az Engedélyezve **lehetőséget.**
1. Ha szükséges, konfigurálja a további beállításjegyzék-beállításokat, majd válassza az **Áttekintés + létrehozás lehetőséget.**
1. A **beállításjegyzék-példány** üzembe helyezéséhez válassza a Létrehozás lehetőséget.

    :::image type="content" source="media/zone-redundancy/enable-availability-zones-portal.png" alt-text="Zónaredundania engedélyezése a Azure Portal":::

Zónaredundáns replikáció létrehozása:

1. Keresse meg a Prémium szintű tároló-beállításjegyzéket, és válassza a **Replikációk lehetőséget.**
1. A megjelenő térképen válasszon egy zöld hatszöget egy olyan régióban, amely támogatja a zónaredundaniát a Azure Container Registry például az **USA 2. nyugati régiójában.** Vagy válassza **a + Hozzáadás lehetőséget.**
1. A Replikáció **létrehozása ablakban** erősítse meg a **Helyet.** A **Rendelkezésre állási zónákban válassza** az **Engedélyezve** lehetőséget, majd a Létrehozás **lehetőséget.**

    :::image type="content" source="media/zone-redundancy/enable-availability-zones-replication-portal.png" alt-text="Zónaredundáns replikáció engedélyezése a Azure Portal":::

## <a name="create-a-zone-redundant-registry---template"></a>Zónaredundáns beállításjegyzék létrehozása – sablon

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Szükség esetén az [az group create](/cli/azure/group#az_group_create) paranccsal hozzon létre egy [](../availability-zones/az-region.md) erőforráscsoportot a regisztrációs adatbázishoz egy olyan régióban, amely támogatja a rendelkezésre állási zónákat a Azure Container Registry, például *eastus* régióban. A sablon ezt a régiót használja a beállításjegyzék helyének beállításához.

```azurecli
az group create --name <resource-group-name> --location eastus
```

### <a name="deploy-the-template"></a>A sablon üzembe helyezése 

Az alábbi Resource Manager hozhat létre zónaredundáns, georeplikált beállításjegyzéket. A sablon alapértelmezés szerint engedélyezi a zónaredundaniát a beállításjegyzékben és egy regionális replikában. 

Másolja az alábbi tartalmat egy új fájlba, és mentse egy fájlnévvel(például: `registryZone.json` ).

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

Futtassa a következő [az deployment group create parancsot](/cli/azure/group/deployment#az_group_deployment_create) a regisztrációs adatbázis létrehozásához az előző sablonfájl használatával. Ahol jelezve van, adja meg a következőt:

* egy egyedi beállításjegyzék-nevet, vagy paraméterek nélkül telepítheti a sablont, és az egyedi nevet hoz létre
* a rendelkezésre állási zónákat támogató replika helye, például *westus2*

```azurecli
az deployment group create \
  --resource-group <resource-group-name> \
  --template-file registryZone.json \
  --parameters acrName=<registry-name> acrReplicaLocation=<replica-location>
```

A parancs kimenetében jegyezze fel `zoneRedundancy` a beállításjegyzék és a replika tulajdonságát. Ha engedélyezve van, minden erőforrás zónaredundáns:

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

## <a name="next-steps"></a>Következő lépések

* További információ a [rendelkezésre állási zónákat támogató régiókról.](../availability-zones/az-region.md)
* További információ a megbízhatóságra való [felépítésről az](/azure/architecture/framework/resiliency/overview) Azure-ban.

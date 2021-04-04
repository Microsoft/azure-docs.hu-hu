---
title: Üzembehelyezési adatok titkosítása
description: Ismerje meg a tároló-példány erőforrásainak megőrzött adatainak titkosítását, valamint az adattitkosítást az ügyfél által felügyelt kulccsal
ms.topic: article
ms.date: 01/17/2020
author: macolso
ms.author: macolso
ms.openlocfilehash: 1b73ce5c994231a1c7b2f26ad702f2ad5880ba44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94686276"
---
# <a name="encrypt-deployment-data"></a>Üzembehelyezési adatok titkosítása

Amikor a felhőben Azure Container Instances (ACI) erőforrásait futtatja, az ACI szolgáltatás összegyűjti és megőrzi a tárolókkal kapcsolatos adatokat. Az ACI automatikusan titkosítja ezeket az adatfájlokat, amikor a felhőben is megmarad. Ezzel a titkosítással megvédheti adatait, hogy megfeleljen a szervezete biztonsági és megfelelőségi kötelezettségvállalásainak. Az ACI lehetővé teszi az adatok saját kulccsal történő titkosítását is, így az ACI-környezetekhez kapcsolódó adatok nagyobb mértékben szabályozhatók.

## <a name="about-aci-data-encryption"></a>Tudnivalók az ACI-adattitkosításról 

Az ACI-ban tárolt adatai titkosítva vannak, és a 256 bites AES-titkosítással lettek visszafejtve. Az összes ACI üzemelő példány esetében engedélyezve van, és nem kell módosítania az üzemelő példányt vagy tárolókat a titkosítás kihasználása érdekében. Ez magában foglalja az üzembe helyezésre vonatkozó metaadatokat, a környezeti változókat, a tárolókban átadott kulcsokat, valamint a tárolók leállítása után megőrzött naplókat, így továbbra is megtekintheti őket. A titkosítás nem befolyásolja a tároló csoport teljesítményét, és a titkosításhoz nincs további díj.

## <a name="encryption-key-management"></a>Titkosítási kulcsok kezelése

A Microsoft által felügyelt kulcsokat a tároló adatai titkosítására használhatja, vagy a titkosítást a saját kulcsaival is kezelheti. A következő táblázat összehasonlítja ezeket a beállításokat: 

|    |    Microsoft által felügyelt kulcsok     |     Felhasználó által kezelt kulcsok     |
|----|----|----|
|    **Titkosítási/visszafejtési műveletek**    |    Azure    |    Azure    |
|    **Kulcstároló**    |    Microsoft Key Store    |    Azure Key Vault    |
|    **Kulcs rotációs felelőssége**    |    Microsoft    |    Ügyfél    |
|    **Kulcs elérése**    |    Csak Microsoft    |    Microsoft, ügyfél    |

A dokumentum többi része ismerteti azokat a lépéseket, amelyek szükségesek az ACI üzembe helyezési adatoknak a kulccsal való titkosításához (az ügyfél által felügyelt kulcs). 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="encrypt-data-with-a-customer-managed-key"></a>Az adattitkosítás ügyfél által felügyelt kulccsal

### <a name="create-service-principal-for-aci"></a>Egyszerű szolgáltatásnév létrehozása az ACI-hoz

Első lépésként győződjön meg arról, hogy az [Azure-bérlő](../active-directory/develop/quickstart-create-new-tenant.md) rendelkezik a Azure Container instances szolgáltatás engedélyeinek megadásához hozzárendelt egyszerű szolgáltatással. 

> [!IMPORTANT]
> A következő parancs futtatásához és az egyszerű szolgáltatás létrehozásához ellenőrizze, hogy rendelkezik-e jogosultsággal a szolgáltatásbeli egyszerű szolgáltatások létrehozásához a bérlőben.
>

Az alábbi CLI-parancs beállítja az ACI SP-t az Azure-környezetben:

```azurecli-interactive
az ad sp create --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9
```

A parancs futtatásának kimenete olyan egyszerű szolgáltatásnevet mutat be, amely a "displayName": "Azure Container instance Service" beállítással lett beállítva.

Ha nem tudja sikeresen létrehozni a szolgáltatásnevet:
* Ellenőrizze, hogy rendelkezik-e engedéllyel a bérlőn
* Ellenőrizze, hogy már létezik-e egy egyszerű szolgáltatásnév a bérlőben az ACI-hoz való üzembe helyezéshez. Ezt az egyszerű szolgáltatásnév futtatásával `az ad sp show --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9` és használatával teheti meg.

### <a name="create-a-key-vault-resource"></a>Key Vault erőforrás létrehozása

Hozzon létre egy Azure Key Vaultt a [Azure Portal](../key-vault/general/quick-create-portal.md), az [Azure CLI](../key-vault/general/quick-create-cli.md)vagy a [Azure PowerShell](../key-vault/general/quick-create-powershell.md)használatával.

A Key Vault tulajdonságaihoz használja a következő irányelveket: 
* Név: Egy egyedi nevet kell megadnia. 
* Előfizetés: Válassza ki az előfizetést.
* Az erőforráscsoport területen válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre újat, és adjon meg egy erőforráscsoport-nevet.
* A Hely legördülő menüből válassza ki a helyet.
* A többi beállítást az alapértelmezett értékekre vagy a további követelmények alapján is elhagyhatja.

> [!IMPORTANT]
> Ha ügyfél által felügyelt kulcsokat használ egy ACI-telepítési sablon titkosításához, akkor a következő két tulajdonságot érdemes beállítani a kulcstartón, a Soft delete parancsot, és ne törölje a kiürítést. Ezek a tulajdonságok alapértelmezés szerint nincsenek engedélyezve, de a PowerShell vagy az Azure CLI használatával is engedélyezhető egy új vagy meglévő kulcstartón.

### <a name="generate-a-new-key"></a>Új kulcs létrehozása 

Miután létrehozta a kulcstartót, navigáljon a Azure Portal található erőforráshoz. Az erőforrás panel bal oldali navigációs menüjének beállítások területén kattintson a **kulcsok** elemre. A "kulcsok" nézetben kattintson a "Létrehozás/Importálás" lehetőségre új kulcs létrehozásához. A kulcs egyedi nevét és egyéb beállításait a követelmények alapján használhatja. 

![Új kulcs létrehozása](./media/container-instances-encrypt-data/generate-key.png)

### <a name="set-access-policy"></a>Hozzáférési szabályzat beállítása

Hozzon létre egy új hozzáférési szabályzatot, amely lehetővé teszi, hogy az ACI szolgáltatás hozzáférjen a kulcshoz.

* Miután létrehozta a kulcsot, a Key Vault erőforrás-paneljén a beállítások területen kattintson a **hozzáférési házirendek** elemre.
* A Key vaulthoz tartozó hozzáférési szabályzatok lapon kattintson a **hozzáférési házirend hozzáadása** lehetőségre.
* Adja meg a kulcs *engedélyeit* a **beolvasás** és **kicsomagolás kulcs** ![ beállítása kulcs engedélyeinek befoglalásához](./media/container-instances-encrypt-data/set-key-permissions.png)
* Válassza ki a *rendszerbiztonsági tag* lehetőséget, majd válassza az **Azure Container instance Service** elemet.
* Kattintson a **Hozzáadás** gombra a lap alján 

A hozzáférési szabályzatnak ekkor meg kell jelennie a Key Vault hozzáférési házirendjeiben.

![Új hozzáférési szabályzat](./media/container-instances-encrypt-data/access-policy.png)

### <a name="modify-your-json-deployment-template"></a>A JSON-telepítési sablon módosítása

> [!IMPORTANT]
> A központi telepítési adatai ügyfél által felügyelt kulccsal történő titkosítása a legújabb API-verzióban (2019-12-01) érhető el, amely jelenleg ki van vezetve. Adja meg ezt az API-verziót a telepítési sablonban. Ha problémája merül fel, forduljon az Azure ügyfélszolgálatához.

A Key Vault-kulcs és a hozzáférési házirend beállítása után adja hozzá a következő tulajdonságokat az ACI telepítési sablonhoz. További információ az ACI-erőforrások üzembe helyezéséről a sablonnal az [oktatóanyagban: többtárolós csoport üzembe helyezése Resource Manager-sablonnal](./container-instances-multi-container-group.md). 
* A alatt `resources` állítsa be a következőt: `apiVersion` `2019-12-01` .
* A telepítési sablon tároló csoport tulajdonságai szakaszában adja meg a `encryptionProperties` következő értékeket tartalmazó értéket:
  * `vaultBaseUrl`: a Key Vault DNS-neve a Key Vault-erőforrás áttekintés paneljén található a portálon
  * `keyName`: a korábban generált kulcs neve
  * `keyVersion`: a kulcs jelenlegi verziója. Ez úgy érhető el, ha magára a kulcsra kattint (a Key Vault-erőforrás beállítások szakaszában a "kulcsok" alatt)
* A tároló csoport tulajdonságai területen adjon hozzá egy `sku` tulajdonságot értékkel `Standard` . A `sku` tulajdonságot az 2019-12-01-os API-verzióban kell megadni.

A következő kódrészlet ezeket a további tulajdonságokat jeleníti meg a központi telepítési információk titkosításához:

```json
[...]
"resources": [
    {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",    
        "properties": {
            "encryptionProperties": {
                "vaultBaseUrl": "https://example.vault.azure.net",
                "keyName": "acikey",
                "keyVersion": "xxxxxxxxxxxxxxxx"
            },
            "sku": "Standard",
            "containers": {
                [...]
            }
        }
    }
]
```

A következő egy teljes sablon, amely a sablon alapján van adaptálva [: többtárolós csoport üzembe helyezése Resource Manager-sablonnal](./container-instances-multi-container-group.md). 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerGroupName": {
      "type": "string",
      "defaultValue": "myContainerGroup",
      "metadata": {
        "description": "Container Group name."
      }
    }
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "mcr.microsoft.com/azuredocs/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "mcr.microsoft.com/azuredocs/aci-tutorial-sidecar"
  },
  "resources": [
    {
      "name": "[parameters('containerGroupName')]",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "encryptionProperties": {
            "vaultBaseUrl": "https://example.vault.azure.net",
            "keyName": "acikey",
            "keyVersion": "xxxxxxxxxxxxxxxx"
        },
        "sku": "Standard",  
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                },
                {
                  "port": 8080
                }
              ]
            }
          },
          {
            "name": "[variables('container2name')]",
            "properties": {
              "image": "[variables('container2image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            },
            {
                "protocol": "tcp",
                "port": "8080"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
    "containerIPv4Address": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', parameters('containerGroupName'))).ipAddress.ip]"
    }
  }
}
```

### <a name="deploy-your-resources"></a>Az erőforrások üzembe helyezése

Ha létrehozta és szerkesztette a sablonfájlt az asztalon, feltöltheti azt a Cloud Shell könyvtárba a fájl húzásával. 

Hozzon létre egy erőforráscsoportot az [az group create][az-group-create] paranccsal.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Telepítse a sablont az az [Deployment Group Create][az-deployment-group-create] paranccsal.

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file deployment-template.json
```

Néhány másodpercen belül meg kell kapnia az Azure kezdeti válaszát. Miután az üzembe helyezés befejeződött, az ACI szolgáltatás által megőrzött összes adat titkosítva lesz a megadott kulccsal.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-deployment-group-create]: /cli/azure/deployment/group/#az-deployment-group-create

---
title: Üzembehelyezési adatok titkosítása
description: Tudnivalók a tárolópéldány-erőforrásokban megőrzött adatok titkosításával és az adatok ügyfél által felügyelt kulccsal való titkosításával kapcsolatban
ms.topic: article
ms.date: 01/17/2020
author: macolso
ms.author: macolso
ms.openlocfilehash: 23c81aeab3bf6e9ee7f2d89fbdf8def20dab4aa7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790868"
---
# <a name="encrypt-deployment-data"></a>Üzembehelyezési adatok titkosítása

Ha a Azure Container Instances (ACI) erőforrásokat futtat a felhőben, az ACI szolgáltatás gyűjti és tárolja a tárolókhoz kapcsolódó adatokat. Az ACI automatikusan titkosítja az adatokat, amikor megőrzik őket a felhőben. Ez a titkosítás védi az adatokat, hogy megfeleljen a szervezet biztonsági és megfelelőségi kötelezettségvállalásoknak. Az ACI azt is lehetővé teszi, hogy az adatokat a saját kulcsával titkosítsa, így nagyobb mértékben szabályozhatja az ACI üzemelő példányokkal kapcsolatos adatokat.

## <a name="about-aci-data-encryption"></a>Az ACI-adattitkosítás 

Az ACI-ban tárolt adatok titkosítása és visszafejtése 256 bites AES-titkosítással történik. Minden ACI-üzemelő példányhoz engedélyezve van, és nem kell módosítania az üzemelő példányokat vagy tárolókat ahhoz, hogy kihasználja ezt a titkosítást. Ezek közé tartoznak az üzembe helyezés metaadatai, a környezeti változók, a tárolókba átadott kulcsok, valamint a tárolók leállását követően megőrzött naplók, hogy továbbra is láthatóak maradnak. A titkosítás nincs hatással a tárolócsoport teljesítményére, és a titkosítás nem jár többletköltséggel.

## <a name="encryption-key-management"></a>Titkosítási kulcskezelés

A tárolóadatok titkosítása Microsoft által felügyelt kulcsokkal, vagy saját kulcsokkal is kezelhető. Az alábbi táblázat ezeket a lehetőségeket hasonlítja össze: 

|    |    Microsoft által felügyelt kulcsok     |     Felhasználó által kezelt kulcsok     |
|----|----|----|
|    **Titkosítási/visszafejtési műveletek**    |    Azure    |    Azure    |
|    **Kulcstároló**    |    Microsoft-kulcstároló    |    Azure Key Vault    |
|    **Kulcsrotációs felelősség**    |    Microsoft    |    Ügyfél    |
|    **Kulcselérés**    |    Csak Microsoft    |    Microsoft, Customer    |

A dokumentum további részében az ACI üzembe helyezési adatainak a kulccsal (ügyfél által felügyelt kulccsal) való titkosításához szükséges lépéseket kell tartalmazni. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="encrypt-data-with-a-customer-managed-key"></a>Adatok titkosítása ügyfél által felügyelt kulccsal

### <a name="create-service-principal-for-aci"></a>Szolgáltatásnév létrehozása az ACI-hoz

Első lépésként győződjön meg [](../active-directory/develop/quickstart-create-new-tenant.md) arról, hogy az Azure-bérlőhöz hozzá van rendelve egy szolgáltatásnév, amely engedélyeket biztosít a Azure Container Instances számára. 

> [!IMPORTANT]
> Az alábbi parancs futtatásához és egy szolgáltatásnév sikeres létrehozásához győződjön meg arról, hogy rendelkezik a szolgáltatásnév bérlőben való létrehozásához szükséges engedélyekkel.
>

A következő CLI-parancs beállít egy ACI SP-t az Azure-környezetben:

```azurecli-interactive
az ad sp create --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9
```

A parancs futtatásának kimenetében meg kell jelenni egy szolgáltatásnévnek, amely a "displayName": "Azure Container Instance Service" beállítással lett beállítva.

Ha nem tudja sikeresen létrehozni az egyszerű szolgáltatást:
* győződjön meg arról, hogy rendelkezik erre a bérlőben szükséges engedélyekkel
* Ellenőrizze, hogy létezik-e már szolgáltatásnév a bérlőben az ACI-ban való üzembe helyezéshez. Ezt a szolgáltatásnév futtatásával és `az ad sp show --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9` használatával teheti meg.

### <a name="create-a-key-vault-resource"></a>Erőforrás Key Vault létrehozása

Hozzon létre egy Azure Key Vault [az Azure Portal,](../key-vault/general/quick-create-portal.md) [az Azure CLI](../key-vault/general/quick-create-cli.md)vagy a [Azure PowerShell](../key-vault/general/quick-create-powershell.md)használatával.

A kulcstartó tulajdonságaihoz használja az alábbi irányelveket: 
* Név: Egy egyedi nevet kell megadnia. 
* Előfizetés: Válassza ki az előfizetést.
* Az Erőforráscsoport alatt válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat, és adjon meg egy erőforráscsoport-nevet.
* A Hely legördülő menüből válassza ki a helyet.
* A többi beállítást meghagyhatja az alapértelmezett értéken, vagy további követelmények alapján választhat.

> [!IMPORTANT]
> Ha felhasználó által kezelt kulcsokat használ az ACI üzembehelyző sablon titkosításához, javasoljuk, hogy a következő két tulajdonságot állítsa be a kulcstartón: a Soft Delete (Biztonságos törlés) és a Do Not Purge (Ne törölje a végleges törlést) tulajdonságot. Ezek a tulajdonságok alapértelmezés szerint nincsenek engedélyezve, de egy új vagy meglévő kulcstartón a PowerShell vagy az Azure CLI használatával engedélyezhetők.

### <a name="generate-a-new-key"></a>Új kulcs létrehozása 

A kulcstartó létrehozása után keresse meg az erőforrást a Azure Portal. Az erőforrás panel bal oldali navigációs menüjében, a Beállítások alatt kattintson a Kulcsok **elemre.** A "Kulcsok" nézetben kattintson a "Generate/Import" (Generálás/importálás) elemre egy új kulcs létrehozásához. Ehhez a kulcshoz bármilyen egyedi nevet és egyéb beállításokat használhat a követelmények alapján. 

![Új kulcs létrehozása](./media/container-instances-encrypt-data/generate-key.png)

### <a name="set-access-policy"></a>Hozzáférési szabályzat beállítása

Hozzon létre egy új hozzáférési szabályzatot, amely lehetővé teszi, hogy az ACI szolgáltatás hozzáférjen a kulcshoz.

* A kulcs létrehozása után a Key Vault-erőforrás panelen, a Beállítások alatt kattintson a Hozzáférési **szabályzatok elemre.**
* A kulcstartó "Hozzáférési szabályzatok" lapján kattintson a **Hozzáférési szabályzat hozzáadása elemre.**
* Állítsa be *úgy a kulcsengedélyeket,* hogy tartalmazzák a **Kulcskészlet** be- és **kiírása** ![ kulcsengedélyeket](./media/container-instances-encrypt-data/set-key-permissions.png)
* A *Rendszerbiztonsági tag kiválasztása beállításnál* válassza az **Azure Container Instance Service lehetőséget**
* Kattintson **az alul** található Hozzáadás gombra. 

A hozzáférési szabályzatnak most meg kell lennie a kulcstartó hozzáférési szabályzatában.

![Új hozzáférési szabályzat](./media/container-instances-encrypt-data/access-policy.png)

### <a name="modify-your-json-deployment-template"></a>A JSON üzembe helyezési sablon módosítása

> [!IMPORTANT]
> Az üzembe helyezési adatok ügyfél által felügyelt kulccsal való titkosítása a jelenleg elérhető legújabb API-verzióban (2019. 12. 01.) érhető el. Adja meg ezt az API-verziót az üzembe helyezési sablonban. Ha ezzel kapcsolatban bármilyen probléma ad problémát, vegye fel a Azure ügyfélszolgálata.

Miután beállította a kulcstartókulcsot és a hozzáférési szabályzatot, adja hozzá a következő tulajdonságokat az ACI üzembe helyezési sablonhoz. További információ az ACI-erőforrások sablonnal való üzembe [helyezéséről: Oktatóanyag:](./container-instances-multi-container-group.md)Többtárolós csoport üzembe helyezése Resource Manager sablon használatával. 
* A `resources` alatt állítsa a `apiVersion` beállítását a beállításra. `2019-12-01`
* Az üzembe helyezési sablon tárolócsoport tulajdonságainak szakaszában adjon hozzá egy et, `encryptionProperties` amely a következő értékeket tartalmazza:
  * `vaultBaseUrl`: a kulcstartó DNS-neve a Portálon, a Key Vault-erőforrás áttekintési panelján található
  * `keyName`: a korábban létrehozott kulcs neve
  * `keyVersion`: a kulcs aktuális verziója. Ehhez kattintson magára a kulcsra (a Key Vault-erőforrás Beállítások szakaszában a "Kulcsok" területen)
* A tárolócsoport tulajdonságai alatt adjon hozzá egy `sku` tulajdonságot `Standard` értékkel. A `sku` tulajdonság a 2019-12-01-es API-verzióban szükséges.

Az alábbi sablonrészlet a telepítési adatok titkosításához szükséges további tulajdonságokat mutatja be:

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

Az alábbiakban egy teljes sablon található, amely az Oktatóanyag: Többtárolós csoport üzembe helyezése egy új sablonnal [Resource Manager alapján.](./container-instances-multi-container-group.md) 

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

### <a name="deploy-your-resources"></a>Erőforrások üzembe helyezése

Ha létrehozta és szerkesztette a sablonfájlt az asztalon, a fájlt a saját könyvtárába Cloud Shell a fájl húzásával töltheti fel. 

Hozzon létre egy erőforráscsoportot az [az group create][az-group-create] paranccsal.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Telepítse a sablont az [az deployment group create paranccsal.][az-deployment-group-create]

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file deployment-template.json
```

Néhány másodpercen belül meg kell kapnia az Azure kezdeti válaszát. Az üzembe helyezés befejezése után az ACI szolgáltatás által megőrzött összes kapcsolódó adat a megadott kulccsal lesz titkosítva.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-deployment-group-create]: /cli/azure/deployment/group/#az_deployment_group_create

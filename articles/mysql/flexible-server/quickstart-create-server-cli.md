---
title: 'Gyors útmutató: kiszolgáló létrehozása – Azure CLI-Azure Database for MySQL – rugalmas kiszolgáló'
description: Ez a rövid útmutató azt ismerteti, hogyan használható az Azure CLI egy Azure Database for MySQL rugalmas kiszolgáló Azure-erőforráscsoportbeli létrehozásához.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 9/21/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 65cc3d2fdcbdea934e80a5f0012ca4f3da157ca3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94843434"
---
# <a name="quickstart-create-an-azure-database-for-mysql-flexible-server-using-azure-cli"></a>Gyors útmutató: Azure Database for MySQL rugalmas kiszolgáló létrehozása az Azure CLI használatával

Ez a rövid útmutató bemutatja, hogyan használhatja az [Azure CLI](/cli/azure/get-started-with-azure-cli) -parancsokat a [Azure Cloud Shellban](https://shell.azure.com) egy Azure Database for MySQL rugalmas kiszolgáló öt perc alatt történő létrehozásához. Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

> [!IMPORTANT] 
> Azure Database for MySQL rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell elindítása

A [Azure Cloud Shell](../../cloud-shell/overview.md) egy ingyenes interaktív felület, amellyel a cikkben ismertetett lépéseket futtathatja. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta.

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shell egy külön böngésző lapon is megnyithatja [https://shell.azure.com/bash](https://shell.azure.com/bash) . Válassza a **Másolás** lehetőséget a kód blokkok másolásához, illessze be a Cloud Shellba, majd válassza az **ENTER billentyűt** a futtatásához.

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2,0-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Előfeltételek

Az az [login](/cli/azure/reference-index#az-login) parancs használatával kell bejelentkeznie a fiókjába. Jegyezze fel az **ID** tulajdonságot, amely az Azure-fiók **előfizetés-azonosítójára** utal.

```azurecli-interactive
az login
```

Válassza ki az adott előfizetést a fiókja alatt az [az Account set](/cli/azure/account#az-account-set) parancs használatával. Jegyezze fel az **azonosító** értéket az az **login** kimenetből, amelyet a parancs **előfizetés** argumentumának értékeként kíván használni. Ha több előfizetéssel rendelkezik válassza ki a megfelelő előfizetést, amelyre az erőforrást terhelni szeretné. Az összes előfizetés beszerzéséhez használja [az az Account List](/cli/azure/account#az-account-list)lehetőséget.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-a-flexible-server"></a>Rugalmas kiszolgáló létrehozása

Hozzon létre egy [Azure-erőforráscsoportot](../../azure-resource-manager/management/overview.md) a `az group create` paranccsal, majd hozza létre a MySQL rugalmas kiszolgálót az erőforráscsoport belsejében. Egyedi nevet adjon meg. A következő példában létrehozunk egy `eastus2` nevű erőforráscsoportot a `myresourcegroup` helyen.

```azurecli-interactive
az group create --name myresourcegroup --location eastus2
```

Hozzon létre egy rugalmas kiszolgálót a `az mysql flexible-server create` paranccsal. Egy kiszolgáló több adatbázist tartalmazhat. A következő parancs egy kiszolgálót hoz létre az Azure CLI [helyi környezetében](/cli/azure/local-context)lévő szolgáltatás-alapértékek és-értékek használatával: 

```azurecli
az mysql flexible-server create
```

A létrehozott kiszolgáló az alábbi attribútumokkal rendelkezik: 
- Automatikusan generált kiszolgálónév, rendszergazdai Felhasználónév, rendszergazdai jelszó, erőforráscsoport neve (ha még nincs meghatározva helyi környezetben), és az erőforráscsoporthoz megegyező helyen 
- Szolgáltatás alapértelmezett értékei a fennmaradó kiszolgálói konfigurációkhoz: számítási szint (feltört), számítási méret/SKU (B1MS), biztonsági mentési megőrzési időtartam (7 nap) és MySQL-verzió (5,7)
- Az alapértelmezett kapcsolódási módszer a privát hozzáférés (VNet-integráció) egy automatikusan generált virtuális hálózattal és alhálózattal

> [!NOTE] 
> A kapcsolódási módszer nem módosítható a kiszolgáló létrehozása után. Ha például a létrehozás során a *privát hozzáférés (VNet-integráció)* lehetőséget választotta, akkor a létrehozás után nem lehet *nyilvános hozzáférésre váltani (engedélyezett IP-címek)* . Javasoljuk, hogy hozzon létre egy olyan kiszolgálót, amely privát hozzáféréssel rendelkezik a kiszolgáló biztonságos eléréséhez a VNet-integráció használatával. További információ a privát hozzáférésről a [fogalmakat ismertető cikkben](./concepts-networking.md).

Ha módosítani szeretné az alapértelmezett beállításokat, tekintse meg az Azure CLI [dokumentációját](/cli/azure/mysql/flexible-server) a konfigurálható CLI-paraméterek teljes listájához. 

Néhány példa a kimenetre: 

```json
Command group 'mysql flexible-server' is in preview. It may be changed/removed in a future release.
Creating Resource Group 'groupXXXXXXXXXX'...
Creating new vnet "serverXXXXXXXXXVNET" in resource group "groupXXXXXXXXXX"...
Creating new subnet "serverXXXXXXXXXSubnet" in resource group "groupXXXXXXXXXX" and delegating it to "Microsoft.DBforMySQL/flexibleServers"...
Creating MySQL Server 'serverXXXXXXXXX' in group 'groupXXXXXXXXXX'...
Your server 'serverXXXXXXXXX' is using sku 'Standard_B1ms' (Paid Tier). Please refer to https://aka.ms/mysql-pricing for pricing details
Creating MySQL database 'flexibleserverdb'...
Make a note of your password. If you forget, you would have to reset your password with 'az mysql flexible-server update -n serverXXXXXXXXX -g groupXXXXXXXXXX -p <new-password>'.
{
  "connectionString": "server=serverXXXXXXXXX.mysql.database.azure.com;database=flexibleserverdb;uid=secureusername;pwd=securepasswordstring",
  "databaseName": "flexibleserverdb",
  "host": "serverXXXXXXXXX.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/groupXXXXXXXXXX/providers/Microsoft.DBforMySQL/flexibleServers/serverXXXXXXXXX",
  "location": "East US 2",
  "password": "securepasswordstring",
  "resourceGroup": "groupXXXXXXXXXX",
  "skuname": "Standard_B1ms",
  "subnetId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/groupXXXXXXXXXX/providers/Microsoft.Network/virtualNetworks/serverXXXXXXXXXVNET/subnets/serverXXXXXXXXXSubnet",
  "username": "secureusername",
  "version": "5.7"
}
```

Ha módosítani szeretné az alapértelmezett beállításokat, tekintse meg az Azure CLI [dokumentációját](/cli/azure/mysql/flexible-server) a konfigurálható CLI-paraméterek teljes listájához. 

> [!NOTE]
> A MySQL-hez készült Azure-adatbázis kapcsolatai a 3306-os porton keresztül kommunikálnak. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy nem engedélyezett a kimenő forgalom a 3306-as porton keresztül. Ebben az esetben addig nem tud csatlakozni a kiszolgálóhoz, amíg az informatikai részleg meg nem nyitja a 3306-os portot.

## <a name="get-the-connection-information"></a>Kapcsolatadatok lekérése

A kiszolgálóhoz való kapcsolódáshoz meg kell adnia a gazdagép adatait és a hozzáférési hitelesítő adatokat.

```azurecli-interactive
az mysql flexible-server show --resource-group myresourcegroup --name mydemoserver
```

Az eredmény JSON formátumban van. Jegyezze fel a következőket: **fullyQualifiedDomainName** és **administratorLogin**. Alább látható egy példa a JSON-kimenetre: 

```json
{
  "administratorLogin": "myadminusername",
  "administratorLoginPassword": null,
  "delegatedSubnetArguments": {
    "subnetArmResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/mydemoserverVNET/subnets/mydemoserverSubnet"
  },
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/flexibleServers/mydemoserver",
  "location": "East US 2",
  "name": "mydemoserver",
  "publicNetworkAccess": "Disabled",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 0,
    "name": "Standard_B1ms",
    "tier": "Burstable"
  },
  "storageProfile": {
    "backupRetentionDays": 7,
    "fileStorageSkuName": "Premium_LRS",
    "storageAutogrow": "Disabled",
    "storageIops": 0,
    "storageMb": 10240
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/flexibleServers",
  "version": "5.7"
}
```

## <a name="connect-using-mysql-command-line-client"></a>Kapcsolat a MySQL parancssori ügyféllel

Mivel a rugalmas kiszolgáló *magánhálózati hozzáféréssel (VNet-integrációval)* lett létrehozva, csatlakoznia kell a kiszolgálóhoz egy olyan erőforrásból, amely a kiszolgálóval megegyező VNet belül található. Létrehozhat egy virtuális gépet, és hozzáadhatja azt a létrehozott virtuális hálózathoz. 

A virtuális gép létrehozása után SSH-t telepíthet a gépre, és telepítheti a népszerű ügyféloldali eszközt, **[mysql.exe](https://dev.mysql.com/downloads/)** parancssori eszközt.

A mysql.exe használatával kapcsolódjon az alábbi paranccsal. Cserélje le az értékeket a tényleges kiszolgáló nevére és jelszavára. 

```bash
 mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha ezekre az erőforrásokra már nincs szüksége más gyorsútmutatókhoz/oktatóanyagokhoz, a következő paranccsal törölheti őket:

```azurecli-interactive
az group delete --name myresourcegroup
```

Ha csak az újonnan létrehozott kiszolgálót szeretné törölni, futtathatja a `az mysql server delete` parancsot.

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
>[PHP-alapú (Laravel-) Webalkalmazás létrehozása MySQL-sel](tutorial-php-database-app.md)

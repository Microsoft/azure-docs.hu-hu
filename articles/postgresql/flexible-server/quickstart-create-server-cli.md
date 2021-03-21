---
title: 'Gyors útmutató: kiszolgáló létrehozása – Azure CLI-Azure Database for PostgreSQL – rugalmas kiszolgáló'
description: Ez a rövid útmutató azt ismerteti, hogyan használható az Azure CLI egy Azure Database for PostgreSQL rugalmas kiszolgáló Azure-erőforráscsoportbeli létrehozásához.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 9/22/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: d43fab43b05ccfd88308390f58ba48ff9111b295
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94842821"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-flexible-server-using-azure-cli"></a>Gyors útmutató: Azure Database for PostgreSQL rugalmas kiszolgáló létrehozása az Azure CLI használatával

Ez a rövid útmutató bemutatja, hogyan használhatja az [Azure CLI](/cli/azure/get-started-with-azure-cli) -parancsokat a [Azure Cloud Shellban](https://shell.azure.com) egy Azure Database for PostgreSQL rugalmas kiszolgáló öt perc alatt történő létrehozásához. Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

> [!IMPORTANT] 
> Azure Database for PostgreSQL rugalmas kiszolgáló jelenleg előzetes verzióban érhető el.

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

Hozzon létre egy [Azure-erőforráscsoportot](../../azure-resource-manager/management/overview.md) a `az group create` paranccsal, majd hozza létre a PostgreSQL rugalmas kiszolgálóját ebben az erőforráscsoportban. Egyedi nevet adjon meg. A következő példában létrehozunk egy `westus` nevű erőforráscsoportot a `myresourcegroup` helyen.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Hozzon létre egy rugalmas kiszolgálót a `az postgres flexible-server create` paranccsal. Egy kiszolgáló több adatbázist tartalmazhat. A következő parancs egy kiszolgálót hoz létre az Azure CLI [helyi környezetében](/cli/azure/local-context)lévő szolgáltatás-alapértékek és-értékek használatával: 

```azurecli
az postgres flexible-server create
```

A létrehozott kiszolgáló az alábbi attribútumokkal rendelkezik: 
- Automatikusan generált kiszolgálónév, rendszergazdai Felhasználónév, rendszergazdai jelszó, erőforráscsoport neve (ha még nincs meghatározva helyi környezetben), és az erőforráscsoporthoz megegyező helyen 
- Szolgáltatás alapértelmezett értékei a fennmaradó kiszolgálói konfigurációkhoz: számítási réteg (általános célú), számítási méret/SKU (D2s_v3-2 virtuális mag, 8 GB RAM), biztonsági mentési megőrzési idő (7 nap) és PostgreSQL-verzió (12)
- Az alapértelmezett kapcsolódási módszer a privát hozzáférés (VNet-integráció) egy automatikusan generált virtuális hálózattal és alhálózattal

> [!NOTE] 
> A kapcsolódási módszer nem módosítható a kiszolgáló létrehozása után. Ha például a létrehozás során a *privát hozzáférés (VNet-integráció)* lehetőséget választotta, akkor a létrehozás után nem lehet *nyilvános hozzáférésre váltani (engedélyezett IP-címek)* . Javasoljuk, hogy hozzon létre egy olyan kiszolgálót, amely privát hozzáféréssel rendelkezik a kiszolgáló biztonságos eléréséhez a VNet-integráció használatával. További információ a privát hozzáférésről a [fogalmakat ismertető cikkben](./concepts-networking.md).

Ha módosítani szeretné az alapértelmezett beállításokat, tekintse meg az Azure CLI dokumentációját. <!--FIXME --> a konfigurálható CLI-paraméterek teljes listájához. 

> [!NOTE]
> Kapcsolatok Azure Database for PostgreSQL a 5432-es porton keresztüli kommunikációhoz. Ha vállalati hálózaton belülről próbál csatlakozni, lehet, hogy a 5432-as porton keresztüli kimenő forgalom nem engedélyezett. Ha ez a helyzet, akkor nem tud csatlakozni a kiszolgálóhoz, ha az informatikai részleg megnyitja a 5432-es portot.

## <a name="get-the-connection-information"></a>Kapcsolatadatok lekérése

A kiszolgálóhoz való kapcsolódáshoz meg kell adnia a gazdagép adatait és a hozzáférési hitelesítő adatokat.

```azurecli-interactive
az postgres flexible-server show --resource-group myresourcegroup --name mydemoserver
```

Az eredmény JSON formátumban van. Jegyezze fel a következőket: **fullyQualifiedDomainName** és **administratorLogin**.

<!--FIXME-->
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/flexibleServers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "name": "Standard_D2s_v3",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "publicAccess": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 131072
  },
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/flexibleServers",
  "userVisibleState": "Ready",
  "version": "12"
}
```

## <a name="connect-using-postgresql-command-line-client"></a>Kapcsolat a PostgreSQL parancssori ügyféllel

Mivel a rugalmas kiszolgáló *magánhálózati hozzáféréssel (VNet-integrációval)* lett létrehozva, csatlakoznia kell a kiszolgálóhoz egy olyan erőforrásból, amely a kiszolgálóval megegyező VNet belül található. Létrehozhat egy virtuális gépet, és hozzáadhatja azt a létrehozott virtuális hálózathoz. 

A virtuális gép létrehozása után SSH-t telepíthet a gépre, és telepítheti a **[psql](https://www.postgresql.org/download/)** parancssori eszközt.

A psql az alábbi parancs használatával kapcsolódhat. Cserélje le az értékeket a tényleges kiszolgáló nevére és jelszavára. 

```bash
psql -h mydemoserver.postgres.database.azure.com -u mydemouser -p
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha ezekre az erőforrásokra már nincs szüksége más gyorsútmutatókhoz/oktatóanyagokhoz, a következő paranccsal törölheti őket:

```azurecli-interactive
az group delete --name myresourcegroup
```

Ha csak az újonnan létrehozott kiszolgálót szeretné törölni, futtathatja a `az postgres flexible-server delete` parancsot.

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
>[Django-alkalmazás üzembe helyezése App Service és PostgreSQL használatával](tutorial-django-app-service-postgres.md)

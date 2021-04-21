---
title: 'Rövid útmutató: Kiszolgáló létrehozása – Azure CLI – Azure Database for PostgreSQL – Rugalmas kiszolgáló'
description: Ez a rövid útmutató azt ismerteti, hogyan használható az Azure CLI egy rugalmas Azure Database for PostgreSQL kiszolgáló létrehozására egy Azure-erőforráscsoportban.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 9/22/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 320457365a36825564154c36ad843ef665fc2d2c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791552"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-flexible-server-using-azure-cli"></a>Rövid útmutató: Rugalmas Azure Database for PostgreSQL létrehozása az Azure CLI használatával

Ez a rövid útmutató bemutatja, hogyan [](https://shell.azure.com) hozhat létre Azure Cloud Shell rugalmas Azure Database for PostgreSQL öt perc alatt az Azure CLI-parancsokkal. [](/cli/azure/get-started-with-azure-cli) Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

> [!IMPORTANT] 
> Azure Database for PostgreSQL rugalmas kiszolgáló jelenleg előzetes verzióban érhető el.

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell elindítása

A [Azure Cloud Shell](../../cloud-shell/overview.md) egy ingyenes interaktív felület, amely a cikkben található lépések futtatására használható. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta.

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A lapot megnyithatja Cloud Shell böngészőlapon a következő megnyitásával: [https://shell.azure.com/bash](https://shell.azure.com/bash) . Kattintson **a Másolás** gombra a kódblokkok másoláshoz, illessze be a Cloud Shell, majd az **Enter** billentyűt a futtatáshoz.

Ha inkább helyileg szeretné telepíteni és használni a CLI-t, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Előfeltételek

Az az login paranccsal kell bejelentkeznie [a fiókjába.](/cli/azure/reference-index#az_login) Figyelje meg **az id** tulajdonságot, amely az Azure-fiók **előfizetés-azonosítójára** vonatkozik.

```azurecli-interactive
az login
```

Válassza ki az adott előfizetést a fiókjában [az az account set paranccsal.](/cli/azure/account#az_account_set) Jegyezze fel  az az **login** output parancs id értékét, amely a subscription argumentum értékeként lesz használva a parancsban.  Ha több előfizetéssel rendelkezik válassza ki a megfelelő előfizetést, amelyre az erőforrást terhelni szeretné. Az összes előfizetését az [az account list használatával használhatja.](/cli/azure/account#az_account_list)

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-a-flexible-server"></a>Rugalmas kiszolgáló létrehozása

Hozzon létre [egy Azure-erőforráscsoportot](../../azure-resource-manager/management/overview.md) az paranccsal, majd hozza létre a rugalmas `az group create` PostgreSQL-kiszolgálót ebben az erőforráscsoportban. Egyedi nevet adjon meg. A következő példában létrehozunk egy `westus` nevű erőforráscsoportot a `myresourcegroup` helyen.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Hozzon létre egy rugalmas kiszolgálót az `az postgres flexible-server create` paranccsal. Egy kiszolgáló több adatbázist tartalmazhat. A következő parancs létrehoz egy kiszolgálót a szolgáltatás alapértelmezett értékeinek és értékeinek használatával az Azure CLI helyi [környezetében:](/cli/azure/local-context) 

```azurecli
az postgres flexible-server create
```

A létrehozott kiszolgáló az alábbi attribútumokkal rendelkezik: 
- Automatikusan létrehozott kiszolgálónév, rendszergazdai felhasználónév, rendszergazdai jelszó, erőforráscsoport neve (ha még nincs megadva a helyi környezetben), és ugyanazon a helyen, ahol az erőforráscsoport található 
- Szolgáltatás alapértelmezett beállításai a fennmaradó kiszolgálói konfigurációkhoz: számítási szint (általános célú), számítási méret/termékváltozat (D2s_v3 – 2 virtuális mag, 8 GB RAM), biztonsági másolatok megőrzési időtartama (7 nap) és PostgreSQL-verzió (12)
- Az alapértelmezett csatlakozási módszer a privát hozzáférés (VNet-integráció) egy automatikusan létrehozott virtuális hálózattal és alhálózattal

> [!NOTE] 
> A kapcsolati módszer nem módosítható a kiszolgáló létrehozása után. Ha például a létrehozás során a Privát hozzáférés *(VNet-integráció)* beállítást választotta, akkor a létrehozás után nem lehet nyilvános *hozzáférésre (engedélyezett IP-címekre)* módosítani. Javasoljuk, hogy hozzon létre egy privát hozzáféréssel bíró kiszolgálót a kiszolgáló VNet-integrációval való biztonságos eléréséhez. A privát hozzáféréssel kapcsolatos további információkért olvassa el [a következő cikket:](./concepts-networking.md).

Ha módosítani szeretné az alapértelmezett beállításokat, tekintse meg az Azure CLI referenciadokumentációját <!--FIXME --> a konfigurálható CLI-paraméterek teljes listájához. 

> [!NOTE]
> A hálózati Azure Database for PostgreSQL az 5432-es porton keresztül kommunikálnak. Ha vállalati hálózaton belülről próbál csatlakozni, előfordulhat, hogy az 5432-es porton keresztüli kimenő forgalom nem engedélyezett. Ebben az esetben csak akkor tud csatlakozni a kiszolgálóhoz, ha az it-részleg megnyitja az 5432-es portot.

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

## <a name="connect-using-postgresql-command-line-client"></a>Csatlakozás PostgreSQL parancssori ügyféllel

Mivel a rugalmas kiszolgáló privát hozzáféréssel *(VNet-integrációval)* lett létrehozva, csatlakoznia kell a kiszolgálóhoz egy olyan erőforrásról, amely a kiszolgálóval azonos virtuális hálózatban található. Létrehozhat egy virtuális gépet, és hozzáadhatja a létrehozott virtuális hálózathoz. 

A virtuális gép létrehozása után SSH-t telepíthet a gépre, és telepítheti a **[psql](https://www.postgresql.org/download/)** parancssori eszközt.

A psql használatával csatlakozzon az alábbi paranccsal. Cserélje le az értékeket a kiszolgáló tényleges nevére és jelszavára. 

```bash
psql -h mydemoserver.postgres.database.azure.com -u mydemouser -p
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha ezekre az erőforrásokra már nincs szüksége más gyorsútmutatókhoz/oktatóanyagokhoz, a következő paranccsal törölheti őket:

```azurecli-interactive
az group delete --name myresourcegroup
```

Ha csak az újonnan létrehozott kiszolgálót szeretné törölni, futtassa a `az postgres flexible-server delete` parancsot.

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
>[Django-alkalmazás üzembe helyezése a App Service PostgreSQL használatával](tutorial-django-app-service-postgres.md)

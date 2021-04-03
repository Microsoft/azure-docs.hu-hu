---
title: 'Gyors útmutató: Azure Database for MySQL létrehozása az az MySQL up használatával'
description: Útmutató az Azure Database for MySQL-kiszolgáló létrehozásához az Azure CLI (parancssori felület) paranccsal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 3/18/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: d89cc41ed26124ae4ad2e6689be6d59278c3d9da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94542167"
---
# <a name="quickstart-create-an-azure-database-for-mysql-using-a-simple-azure-cli-command---az-mysql-up-preview"></a>Gyors útmutató: Azure Database for MySQL létrehozása egyszerű Azure CLI-parancs használatával – az MySQL up (előzetes verzió)

> [!IMPORTANT]
> Az az [MySQL up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) Azure CLI-parancs előzetes verzióban érhető el.

A MySQL-hez készült Azure Database egy felügyelt szolgáltatás, amely lehetővé teszi a magas rendelkezésre állású MySQL-adatbázisok futtatását, kezelését és skálázását a felhőben. Az Azure CLI az Azure-erőforrások parancssorból vagy parancsfájlokból történő létrehozására és kezelésére szolgál. Ez a rövid útmutató azt ismerteti, hogyan használható az az [MySQL up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) parancs egy Azure Database for MySQL kiszolgáló létrehozásához az Azure CLI használatával. A kiszolgáló létrehozása mellett a `az mysql up` parancs létrehoz egy minta-adatbázist, egy gyökérszintű felhasználót az adatbázisban, megnyitja az Azure-szolgáltatások tűzfalát, és létrehozza az alapértelmezett tűzfalszabályok az ügyfélszámítógépen. Ez segít a fejlesztési folyamat felgyorsításában.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

Ehhez a cikkhez az Azure CLI 2,0-es vagy újabb verzióját kell futtatnia helyileg. A telepített verziók megtekintéséhez futtassa az `az --version` parancsot. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

Az az [login](/cli/azure/authenticate-azure-cli) parancs használatával kell bejelentkeznie a fiókjába. Jegyezze fel a megfelelő előfizetésnév parancskimenetéből az **id** tulajdonságot.

```azurecli
az login
```

Ha több előfizetéssel rendelkezik válassza ki a megfelelő előfizetést, amelyre az erőforrást terhelni szeretné. Válassza ki a megadott előfizetés-azonosítót a fiókja alatt az [az account set](/cli/azure/account) paranccsal. **Az előfizetés-azonosító tulajdonságot** az előfizetés-azonosító helyőrzőbe írja be az előfizetéshez tartozó **bejelentkezési** kimenetből.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-mysql-server"></a>Azure-adatbázis létrehozása MySQL-kiszolgálóhoz

A parancsok használatához telepítse a [db-up](/cli/azure/ext/db-up) bővítményt. Ha a rendszer hibát ad vissza, győződjön meg róla, hogy telepítette az Azure CLI legújabb verzióját. Lásd: az [Azure CLI telepítése](/cli/azure/install-azure-cli).

```azurecli
az extension add --name db-up
```

Hozzon létre egy Azure Database for MySQL-kiszolgálót a következő parancs használatával:

```azurecli
az mysql up
```

A kiszolgáló a következő alapértelmezett értékekkel jön létre (kivéve, ha manuálisan felülbírálja őket):

**Beállítás** | **Alapértelmezett érték** | **Leírás**
---|---|---
server-name | Rendszer által generált | Egy egyedi név, amely azonosítja a MySQL-kiszolgálóhoz készült Azure-adatbázist.
resource-group | Rendszer által generált | Egy új Azure-erőforráscsoport.
sku-name | GP_Gen5_2 | A termékváltozat neve. A {tarifacsomag}\_{számítási generáció}\_{virtuális magok} mintát követi rövidített módon. Az alapértelmezett érték egy általános célú Gen5-kiszolgáló 2 virtuális mag. A szintekkel kapcsolatos további információkért tekintse meg a [díjszabási](https://azure.microsoft.com/pricing/details/mysql/) oldalunkat.
backup-retention | 7 | Az az időtartam, ameddig egy biztonsági mentést meg kell őrizni. A mértékegysége a nap.
geo-redundant-backup | Disabled (Letiltva) | Azt adja meg, hogy a georedundáns biztonsági mentést engedélyezni kell-e ehhez a kiszolgálóhoz.
location | westus2 | A kiszolgáló Azure-helye.
ssl-enforcement | Engedélyezve | Azt jelzi, hogy engedélyezve van-e az SSL, vagy sem ehhez a kiszolgálóhoz.
storage-size | 5120 | A kiszolgáló tárkapacitása (megabájtban megadva).
version | 5.7 | A MySQL legújabb főverziója.
admin-user | Rendszer által generált | A rendszergazda bejelentkezéshez használt felhasználóneve.
admin-password | Rendszer által generált | A rendszergazda felhasználó jelszava.

> [!NOTE]
> További információ a `az mysql up` parancsról és a további paraméterekről: az [Azure CLI dokumentációja](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up).

A kiszolgáló létrehozása után a következő beállításokkal rendelkezik:

- Létrejön egy "devbox" nevű tűzfalszabály. Az Azure CLI megpróbálja felderíteni annak a számítógépnek az IP-címét, `az mysql up` amelyről a parancs fut, és engedélyezi ezt az IP-címet.
- "Az Azure-szolgáltatásokhoz való hozzáférés engedélyezése" beállítás be értékre van állítva. Ezzel a beállítással konfigurálható a kiszolgáló tűzfala, hogy fogadja az összes Azure-erőforrás kapcsolatait, beleértve az előfizetésben nem szereplő erőforrásokat is.
- A `wait_timeout` paraméter értéke 8 óra
- A rendszer létrehoz egy "sampledb" nevű üres adatbázist.
- Létrejön egy "root" nevű új felhasználó, amely jogosultsággal rendelkezik a "sampledb" létrehozásához.

> [!NOTE]
> A Azure Database for MySQL a 3306-es porton keresztül kommunikál. Ha vállalati hálózaton belülről csatlakozik, előfordulhat, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 3306-as porton keresztül. Az IT-részleg az 3306-as portot nyitja meg a kiszolgálóhoz való csatlakozáshoz.

## <a name="get-the-connection-information"></a>Kapcsolatadatok lekérése

A `az mysql up` parancs befejezése után a rendszer visszaadja a népszerű programozási nyelvekhez tartozó kapcsolódási karakterláncok listáját. Ezek a csatlakozási karakterláncok előre konfigurálva vannak az újonnan létrehozott Azure Database for MySQL-kiszolgáló adott attribútumaival.

A kapcsolati karakterláncok újbóli listázásához használja az az [MySQL show-kapcsolat-string](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-show-connection-string) parancsot.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A következő parancs használatával törölje a gyors útmutatóban létrehozott összes erőforrást. Ez a parancs törli a Azure Database for MySQL kiszolgálót és az erőforráscsoportot.

```azurecli
az mysql down --delete-group
```

Ha csak az újonnan létrehozott kiszolgálót szeretné törölni, futtathatja az [az MySQL Down](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-down) parancsot.

```azurecli
az mysql down
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [MySQL-adatbázis tervezése az Azure CLI-vel](./tutorial-design-database-using-cli.md)

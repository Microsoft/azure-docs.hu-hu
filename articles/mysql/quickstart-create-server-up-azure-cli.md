---
title: 'Rövid útmutató: Azure Database for MySQL létrehozása az az mysql up használatával'
description: Rövid útmutató a Azure Database for MySQL létrehozásához az Azure CLI (parancssori felület) up parancsával.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 3/18/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: d753061c6141dd0ca75415cab5502e7fa350cd90
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873540"
---
# <a name="quickstart-create-an-azure-database-for-mysql-using-a-simple-azure-cli-command---az-mysql-up-preview"></a>Rövid útmutató: Azure Database for MySQL létrehozása egyszerű Azure CLI-paranccsal – az mysql up (előzetes verzió)

> [!IMPORTANT]
> Az [az mysql up](/cli/azure/mysql#az_mysql_up) Azure CLI-parancs előzetes verzióban érhető el.

A MySQL-hez készült Azure Database egy felügyelt szolgáltatás, amely lehetővé teszi a magas rendelkezésre állású MySQL-adatbázisok futtatását, kezelését és skálázását a felhőben. Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel való létrehozására és kezelésére szolgál. Ez a rövid útmutató bemutatja, hogyan hozhat létre egy új Azure Database for MySQL az Azure CLI használatával az [az mysql up](/cli/azure/mysql#az_mysql_up) paranccsal. A kiszolgáló létrehozása mellett a parancs létrehoz egy mintaadatbázist, egy gyökér szintű felhasználót az adatbázisban, megnyitja az Azure-szolgáltatások tűzfalát, és alapértelmezett tűzfalszabályokat hoz létre az `az mysql up` ügyfélszámítógép számára. Ez segít felgyorsítni a fejlesztési folyamatot.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

Ehhez a cikkhez az Azure CLI 2.0-s vagy újabb verziójára lesz szükség helyileg. A telepített verziók megtekintéséhez futtassa az `az --version` parancsot. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

Az az login paranccsal jelentkezzen be [a fiókjába.](/cli/azure/authenticate-azure-cli) Jegyezze fel a megfelelő előfizetésnév parancskimenetéből az **id** tulajdonságot.

```azurecli
az login
```

Ha több előfizetéssel rendelkezik válassza ki a megfelelő előfizetést, amelyre az erőforrást terhelni szeretné. Válassza ki a megadott előfizetés-azonosítót a fiókja alatt az [az account set](/cli/azure/account) paranccsal. Az **előfizetése az** **login** output tulajdonságát helyettesítse be az előfizetés azonosítójának helyőrzőjére.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-mysql-server"></a>Azure-adatbázis létrehozása MySQL-kiszolgálóhoz

A parancsok használatával telepítse [az db-up bővítményt.](/cli/azure/) Ha hibát ad vissza, győződjön meg arról, hogy az Azure CLI legújabb verziója van telepítve. Lásd: [Az Azure CLI telepítése.](/cli/azure/install-azure-cli)

```azurecli
az extension add --name db-up
```

Hozzon létre Azure Database for MySQL-kiszolgálót a következő paranccsal:

```azurecli
az mysql up
```

A kiszolgáló a következő alapértelmezett értékekkel jön létre (kivéve, ha manuálisan felülbírálja őket):

**Beállítás** | **Alapértelmezett érték** | **Leírás**
---|---|---
server-name | Rendszer generálva | Egy egyedi név, amely azonosítja a MySQL-kiszolgálóhoz készült Azure-adatbázist.
resource-group | Rendszer generálva | Egy új Azure-erőforráscsoport.
sku-name | GP_Gen5_2 | A termékváltozat neve. A {tarifacsomag}\_{számítási generáció}\_{virtuális magok} mintát követi rövidített módon. Az alapértelmezett érték egy általános célú Gen5-kiszolgáló 2 virtuális maggal. A [tarifacsomagokkal kapcsolatos](https://azure.microsoft.com/pricing/details/mysql/) további információkért tekintse meg a díjszabási oldalt.
backup-retention | 7 | Az az időtartam, ameddig egy biztonsági mentést meg kell őrizni. A mértékegysége a nap.
geo-redundant-backup | Disabled (Letiltva) | Azt adja meg, hogy a georedundáns biztonsági mentést engedélyezni kell-e ehhez a kiszolgálóhoz.
location | westus2 | A kiszolgáló Azure-helye.
ssl-enforcement | Engedélyezve | Azt határozza meg, hogy engedélyezni kell-e az SSL-t ehhez a kiszolgálóhoz.
storage-size | 5120 | A kiszolgáló tárkapacitása (megabájtban megadva).
version | 5.7 | A MySQL legújabb főverziója.
admin-user | Rendszer generálva | A rendszergazda bejelentkezéshez használt felhasználóneve.
admin-password | Rendszer generálva | A rendszergazda felhasználó jelszava.

> [!NOTE]
> A parancsról és annak további paramétereiről az `az mysql up` [Azure CLI dokumentációjában talál további információt.](/cli/azure/mysql#az_mysql_up)

A kiszolgáló létrehozása után a következő beállításokat tartalmazza:

- Létrejön egy "devbox" nevű tűzfalszabály. Az Azure CLI megkísérli észlelni annak a gépnek az IP-címét, amelyről a parancs fut, és `az mysql up` engedélyezi ezt az IP-címet.
- Az "Azure-szolgáltatásokhoz való hozzáférés engedélyezése" beállítás BE van állítva. Ez a beállítás úgy konfigurálja a kiszolgáló tűzfalát, hogy az minden Azure-erőforrásból fogadjon kapcsolatokat, beleértve az előfizetésen kívül található erőforrásokat is.
- A `wait_timeout` paraméter értéke 8 óra
- Létrejön egy "sampledb" nevű üres adatbázis
- Létrejön egy új, "root" nevű felhasználó, aki rendelkezik a "sampledb" azonosítóhoz szükséges jogosultságokkal

> [!NOTE]
> Azure Database for MySQL a 3306-os porton keresztül kommunikál. Vállalati hálózaton belülről való csatlakozáskor előfordulhat, hogy a hálózati tűzfal nem tudja a 3306-os porton keresztül kimenő forgalmat. Az IT-részlegnek meg kell nyitnia a 3306-os portot a kiszolgálóhoz való csatlakozáshoz.

## <a name="get-the-connection-information"></a>Kapcsolatadatok lekérése

A parancs befejezése után a rendszer visszaadja a népszerű programozási nyelvek kapcsolati sztringek `az mysql up` listáját. Ezek a kapcsolati sztringek előre konfigurálva vannak az újonnan létrehozott Azure Database for MySQL attribútumokkal.

Az az [mysql show-connection-string paranccsal](/cli/azure/mysql#az_mysql_show_connection_string) újra listálhatja ezeket a kapcsolati sztringeket.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A rövid útmutatóban létrehozott összes erőforrást az alábbi paranccsal lehet megtisztítani. Ez a parancs törli a Azure Database for MySQL kiszolgálót és az erőforráscsoportot.

```azurecli
az mysql down --delete-group
```

Ha csak az újonnan létrehozott kiszolgálót szeretné törölni, futtathatja [az az mysql down parancsot.](/cli/azure/mysql#az_mysql_down)

```azurecli
az mysql down
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [MySQL-adatbázis tervezése az Azure CLI használatával](./tutorial-design-database-using-cli.md)

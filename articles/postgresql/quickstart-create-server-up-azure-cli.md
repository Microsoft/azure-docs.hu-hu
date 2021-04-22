---
title: 'Rövid útmutató: Kiszolgáló létrehozása – az postgres up – Azure Database for PostgreSQL – Egyetlen kiszolgáló'
description: 'Rövid útmutató: Azure Database for PostgreSQL – Egykiszolgálós kiszolgáló létrehozása az Azure CLI (parancssori felület) up paranccsal.'
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/06/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: f0f0dc56dcaad73ff945d319cf98dc30483ee07e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875088"
---
# <a name="quickstart-use-an-azure-cli-command-az-postgres-up-preview-to-create-an-azure-database-for-postgresql---single-server"></a>Rövid útmutató: Az az postgres up (előzetes verzió) Azure CLI-paranccsal hozzon létre Azure Database for PostgreSQL – Egyetlen kiszolgáló

> [!IMPORTANT]
> Az [az postgres up](/cli/azure/postgres#az_postgres_up) Azure CLI-parancs előzetes verzióban érhető el.

A PostgreSQL-hez készült Azure Database felügyelt szolgáltatás, amely lehetővé teszi a magas rendelkezésre állású PostgreSQL-adatbázisok futtatását, kezelését és skálázását a felhőben. Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez a rövid útmutató bemutatja, hogyan hozhat létre egy új Azure Database for PostgreSQL az Azure CLI használatával az [az postgres up](/cli/azure/postgres#az_postgres_up) paranccsal. A kiszolgáló létrehozása mellett a parancs létrehoz egy mintaadatbázist, egy gyökér szintű felhasználót az adatbázisban, megnyitja az Azure-szolgáltatások tűzfalát, és létrehozza az alapértelmezett tűzfalszabályokat az `az postgres up` ügyfélszámítógép számára. Ezek az alapértelmezések segítenek felgyorsítni a fejlesztési folyamatot.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

Ehhez a cikkhez az Azure CLI 2.0-s vagy újabb verziójára lesz szükség helyileg. A telepített verziók megtekintéséhez futtassa az `az --version` parancsot. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

Az az login paranccsal kell bejelentkeznie [a fiókjába.](/cli/azure/authenticate-azure-cli) Jegyezze fel **a megfelelő** előfizetés nevének parancskimenetéből származó ID tulajdonságot.

```azurecli
az login
```

Ha több előfizetéssel rendelkezik válassza ki a megfelelő előfizetést, amelyre az erőforrást terhelni szeretné. Válassza ki a megadott előfizetés-azonosítót a fiókja alatt az [az account set](/cli/azure/account) paranccsal. Helyettesítse **be** az **előfizetése az login** output parancsában található előfizetés-azonosító tulajdonságot az előfizetés-azonosító helyőrzőjére.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Azure-adatbázis létrehozása PostgreSQL-kiszolgálóhoz

A parancsok használatával telepítse [az db-up bővítményt.](/cli/azure/) Ha hibát ad vissza, győződjön meg arról, hogy az Azure CLI legújabb verziója van telepítve. Lásd: [Az Azure CLI telepítése.](/cli/azure/install-azure-cli)

```azurecli
az extension add --name db-up
```

Hozzon létre Azure Database for PostgreSQL-kiszolgálót a következő paranccsal:

```azurecli
az postgres up
```

A kiszolgáló a következő alapértelmezett értékekkel jön létre (kivéve, ha manuálisan felülírja őket):

**Beállítás** | **Alapértelmezett érték** | **Leírás**
---|---|---
server-name | Rendszer generálva | Egy egyedi név, amely az Azure Database for PostgreSQL-kiszolgálót azonosítja.
resource-group | Rendszer generálva | Egy új Azure-erőforráscsoport.
sku-name | GP_Gen5_2 | A termékváltozat neve. A {tarifacsomag}\_{számítási generáció}\_{virtuális magok} mintát követi rövidített módon. Az alapértelmezett érték egy általános célú Gen5-kiszolgáló 2 virtuális maggal. A [csomagokkal kapcsolatos](https://azure.microsoft.com/pricing/details/postgresql/) további információkért tekintse meg a díjszabási oldalt.
backup-retention | 7 | A biztonsági másolat megőrzésének ideje. A mértékegysége a nap.
geo-redundant-backup | Disabled (Letiltva) | Azt adja meg, hogy a georedundáns biztonsági mentést engedélyezni kell-e ehhez a kiszolgálóhoz.
location | westus2 | A kiszolgáló Azure-helye.
ssl-enforcement | Disabled (Letiltva) | Azt határozza meg, hogy a TLS/SSL engedélyezve legyen-e ehhez a kiszolgálóhoz.
storage-size | 5120 | A kiszolgáló tárkapacitása (megabájtban megadva).
version | 10 | A PostgreSQL főverziója.
admin-user | Rendszer generálva | A rendszergazda felhasználóneve.
admin-password | Rendszer generálva | A rendszergazda felhasználó jelszava.

> [!NOTE]
> A paranccsal és annak további paramétereivel kapcsolatos további információkért tekintse meg `az postgres up` az Azure CLI [dokumentációját.](/cli/azure/postgres#az_postgres_up)

A kiszolgáló létrehozása után a következő beállításokat tartalmazza:

- Létrejön egy "devbox" nevű tűzfalszabály. Az Azure CLI megkísérli észlelni annak a gépnek az IP-címét, amelyről a parancs fut, és `az postgres up` engedélyezi ezt az IP-címet.
- Az "Azure-szolgáltatásokhoz való hozzáférés engedélyezése" beállítás BE van állítva. Ez a beállítás úgy konfigurálja a kiszolgáló tűzfalát, hogy minden Azure-erőforrásból fogadja a kapcsolatokat, beleértve az előfizetésen kívül található erőforrásokat is.
- Létrejön egy "sampledb" nevű üres adatbázis
- Létrejön egy új, "root" nevű felhasználó a sampledb-hez szükséges jogosultságokkal

> [!NOTE]
> Azure Database for PostgreSQL 5432-es porton keresztül kommunikál. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 5432-es porton keresztül. Az it-részlegnek meg kell nyitnia az 5432-es portot a kiszolgálóhoz való csatlakozáshoz.

## <a name="get-the-connection-information"></a>Kapcsolatadatok lekérése

A parancs befejezése után a rendszer visszaadja a népszerű programnyelvek kapcsolati sztringek `az postgres up` listáját. Ezek a kapcsolati sztringek előre konfigurálva vannak az újonnan létrehozott Azure Database for PostgreSQL attribútumokkal.

Az az [postgres show-connection-string](/cli/azure/postgres#az_postgres_show_connection_string) paranccsal újra listálhatja ezeket a kapcsolati sztringeket.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A rövid útmutatóban létrehozott összes erőforrást az alábbi paranccsal lehet megtisztítani. Ez a parancs törli a Azure Database for PostgreSQL kiszolgálót és az erőforráscsoportot.

```azurecli
az postgres down --delete-group
```

Ha csak az újonnan létrehozott kiszolgálót szeretné törölni, futtathatja [az az postgres down parancsot.](/cli/azure/postgres#az_postgres_down)

```azurecli
az postgres down
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Adatbázis migrálása exportálással és importálással](./howto-migrate-using-export-and-import.md)

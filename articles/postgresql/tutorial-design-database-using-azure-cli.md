---
title: 'Oktatóanyag: Azure Database for PostgreSQL – egyetlen kiszolgáló tervezése – Azure CLI'
description: Ez az oktatóanyag bemutatja, hogyan hozhatja létre, konfigurálhatja és kérdezheti le az első Azure Database for PostgreSQL egyetlen kiszolgálót az Azure CLI használatával.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.custom: mvc, devx-track-azurecli
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 06/25/2019
ms.openlocfilehash: 019e6e738ea312b7e6a16c44354c7dcd54e24f2f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93331896"
---
# <a name="tutorial-design-an-azure-database-for-postgresql---single-server-using-azure-cli"></a>Oktatóanyag: Azure Database for PostgreSQL egyetlen kiszolgáló megtervezése az Azure CLI használatával 
Ebben az oktatóanyagban az Azure CLI (parancssori felület) és egyéb segédprogramok segítségével a következőket sajátíthatja el:
> [!div class="checklist"]
> * Azure-adatbázis létrehozása PostgreSQL-kiszolgálóhoz
> * A kiszolgáló tűzfalának konfigurálása
> * Adatbázis létrehozása a [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html) segédprogrammal
> * Mintaadatok betöltése
> * Adatok lekérdezése
> * Adatok frissítése
> * Adatok visszaállítása

Az oktatóanyag parancsainak futtatásához használja az Azure Cloud Shellt egy böngészőben, vagy [telepítse az Azure CLI-t]( /cli/azure/install-azure-cli) a számítógépen.

## <a name="prerequisites"></a>Előfeltételek
Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli). 

Ha több előfizetéssel rendelkezik, válassza a megfelelő előfizetést, amelyen az erőforrás megtalálható vagy terhelve van. Válasszon ki egy megadott előfizetés-azonosítót a fiókja alatt az [az account set](/cli/azure/account) parancs segítségével.
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
Hozzon létre egy [Azure-erőforráscsoportot](../azure-resource-manager/management/overview.md) az [az group create](/cli/azure/group) paranccsal. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és csoportként kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy `westus` nevű erőforráscsoportot a `myresourcegroup` helyen.
```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Azure-adatbázis létrehozása PostgreSQL-kiszolgálóhoz
Hozzon létre egy [Azure-adatbázist PostgreSQL- kiszolgálóhoz](overview.md) az [az postgres server create](/cli/azure/postgres/server) paranccsal. A kiszolgáló adatbázisok egy csoportját tartalmazza, amelyeket a rendszer egy csoportként kezel. 

A következő példában létrehozunk egy `mydemoserver` nevű kiszolgálót a `myresourcegroup` erőforráscsoportban `myadmin` kiszolgálói rendszergazdai bejelentkezéssel. A kiszolgáló neve DNS-névbe van leképezve, ezért globálisan egyedinek kell lennie az Azure-ban. A `<server_admin_password>` helyére írja be saját értékét. Ez egy általános célú, Gen 5 kiszolgáló 2 virtuális mag.
```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 9.6
```
Az sku-name paraméter értéke a {tarifacsomag}\_{számítási generáció}\_{virtuális magok} mintát követi, a következő példákban látható módon:
+ `--sku-name B_Gen5_2` az alapszintű, a Gen 5 és a 2 virtuális mag képezi le.
+ `--sku-name GP_Gen5_32` jelentése: Általános célú, 5. generációs és 32 virtuális mag.
+ `--sku-name MO_Gen5_2` jelentése: Memóriaoptimalizált, 5. generációs és 2 virtuális mag.

A [Tarifacsomagok](./concepts-pricing-tiers.md) dokumentumban megtekintheti az érvényes értékeket régiónként és csomagonként.

> [!IMPORTANT]
> A kiszolgáló itt megadott rendszergazdai bejelentkezési nevét és jelszavát kell majd használnia a rövid útmutató későbbi szakaszaiban a kiszolgálóra és az adatbázisaira való bejelentkezéshez. Jegyezze meg vagy jegyezze fel ezt az információt későbbi használatra.

Alapértelmezés szerint a **postgres** adatbázis a kiszolgáló alatt jön létre. A [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) adatbázis egy alapértelmezett adatbázis, amelyet a felhasználók, segédprogramok és külső féltől származó alkalmazások általi használatra szántak. 


## <a name="configure-a-server-level-firewall-rule"></a>Kiszolgálószintű tűzfalszabály konfigurálása

Hozzon létre egy Azure PostgreSQL kiszolgálószintű tűzfalszabályt az [az sql server firewall create](/cli/azure/postgres/server/firewall-rule) paranccsal. Egy kiszolgálószintű tűzfalszabály lehetővé teszi olyan külső alkalmazások számára, mint a [psql](https://www.postgresql.org/docs/9.2/static/app-psql.html), vagy a [PgAdmin](https://www.pgadmin.org/), hogy kapcsolódjon a kiszolgálóhoz az PostgreSQL szolgáltatás tűzfalán keresztül. 

Beállíthat egy olyan tűzfalszabályt, amely lefed egy IP-címtartományt, annak érdekében, hogy csatlakozni tudjon a saját hálózatából. A következő példában az [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule) parancsot használjuk egy olyan `AllowMyIP` tűzfalszabály létrehozásához, amely egyetlen IP-címről engedélyezi a kapcsolódást.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

Ha az Azure PostgreSQL-kiszolgáló hozzáférését a hálózaton belülre szeretné korlátozni, beállíthatja úgy a tűzfalszabályt, hogy csak a vállalati hálózat IP-címtartományáról engedélyezze a hozzáférést.

> [!NOTE]
> Azure PostgreSQL-kiszolgáló az 5432-es porton keresztül kommunikál. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 5432-es porton keresztül. Kérje meg az informatikai részleget, hogy nyissa meg az 5432-es portot az Azure SQL-adatbáziskiszolgálóhoz való csatlakozáshoz.
>

## <a name="get-the-connection-information"></a>Kapcsolatadatok lekérése

A kiszolgálóhoz való kapcsolódáshoz meg kell adnia a gazdagép adatait és a hozzáférési hitelesítő adatokat.
```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mydemoserver
```

Az eredmény JSON formátumban van. Jegyezze fel a következőket: **administratorLogin** és **fullyQualifiedDomainName**.
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"

}
```

## <a name="connect-to-azure-database-for-postgresql-database-using-psql"></a>Csatlakozás az Azure Database for PostgreSQL-hez psql használatával
Ha az ügyfélszámítógépen telepítve van a PostgreSQL, használhatja a [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html)helyi példányát vagy az Azure Cloud Console-t egy Azure PostgreSQL-kiszolgálóhoz való kapcsolódáshoz. Használjuk a psql parancssori segédprogramot az Azure-adatbázis PostgreSQL-kiszolgálóhoz való kapcsolódáshoz.

1. Futtassa a következő psql-parancsot az Azure Database for PostgreSQL-adatbázishoz való kapcsolódáshoz:
   ```
   psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
   ```

   Például a következő parancs a **postgres** nevű alapértelmezett adatbázishoz kapcsolódik a **mydemoserver.postgres.database.azure.com** PostgreSQL-kiszolgálón a hozzáférési hitelesítő adatok használatával. Adja meg a `<server_admin_password>` kiszolgálói rendszergazdai jelszót, amelyet a jelszó megadásakor választott.
  
   ```
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   > [!TIP]
   > Ha inkább URL-elérési utat szeretne használni a postgres-hez való kapcsolódáshoz, az URL-cím kódolja a @ Sign nevet a következővel: `%40` . Például a psql tartozó kapcsolatok karakterlánca a következő:.
   > ```
   > psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres
   > ```

2. Miután csatlakozott a kiszolgálóhoz, hozzon létre egy üres adatbázist, amikor a rendszer erre kéri:
   ```sql
   CREATE DATABASE mypgsqldb;
   ```

3. Amikor a rendszer kéri, hajtsa végre a következő parancsot, hogy az újonnan létrehozott **mypgsqldb** adatbázishoz kapcsolódhasson:
   ```sql
   \c mypgsqldb
   ```

## <a name="create-tables-in-the-database"></a>Táblák létrehozása az adatbázisban
Most, hogy tudja, hogyan csatlakozhat az Azure Database for PostgreSQL szolgáltatáshoz, elvégezhet néhány alapvető feladatot:

Először hozzunk létre egy táblát, és töltsük fel adatokkal. Például hozzunk létre egy táblát a leltáradatok nyomon követéséhez:
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

Az újonnan létrehozott tábla a táblák listájában való megtekintéséhez írja be a következőt:
```sql
\dt
```

## <a name="load-data-into-the-table"></a>Adatok betöltése a táblába
Most, hogy létrehoztuk a táblát, szúrjunk be néhány adatot. A megnyitott parancssori ablakban futtassa a következő lekérdezést néhány adatsor beszúrásához:
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Így két sornyi mintaadat került a korábban létrehozott táblába.

## <a name="query-and-update-the-data-in-the-tables"></a>A táblákban lévő adatok lekérdezése és frissítése
Hajtsa végre a következő lekérdezést a készlettáblában lévő információk lekéréséhez: 
```sql
SELECT * FROM inventory;
```

A készlettáblában található adatokat frissíteni is lehet:
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

A frissített értékeket az adatok lekérésekor tekintheti meg:
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Adatbázis visszaállítása egy korábbi időpontra
Tegyük fel, hogy véletlenül törölt egy táblát. Ez nem olyasvalami, ami könnyen helyreállítható. Az Azure Database for PostgreSQL segítségével bármely olyan időpontra vissza lehet térni, amelyről a kiszolgáló biztonsági mentéssel rendelkezik (ez a biztonsági mentések megőrzési idejéhez megadott beállításoktól függ), és ezt a pontot vissza lehet állítani egy új kiszolgálóra. Az új kiszolgáló segítségével helyreállíthatja a törölt adatokat. 

Az alábbi parancs a mintakiszolgálót egy, a tábla hozzáadása előtti időpontra állítja vissza:
```azurecli-interactive
az postgres server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2017-04-13T13:59:00Z --source-server mydemoserver
```

Az `az postgres server restore` parancshoz a következő paraméterekre van szükség:

| Beállítás | Ajánlott érték | Leírás  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  Az erőforráscsoport, amelyben a forráskiszolgáló található.  |
| name | mydemoserver-restored | A visszaállítási paranccsal létrehozott új kiszolgáló neve. |
| restore-point-in-time | 2017-04-13T13:59:00Z | Válassza ki az időpontot, amelynek az állapotát vissza szeretné állítani. Ennek a dátumnak és időnek a forráskiszolgáló biztonsági mentésének megőrzési időszakán belül kell lennie. ISO8601 dátum- és időformátumot használjon. Használhatja például a saját helyi időzónáját (például `2017-04-13T05:59:00-08:00`), de UTC Zulu formátumot is használhat (`2017-04-13T13:59:00Z`). |
| source-server | mydemoserver | A forráskiszolgáló neve vagy azonosítója, amelyről a visszaállítást végzi. |

Ha egy adott időpontra állít vissza egy kiszolgálót, azzal létrehoz egy újat, amely az eredeti kiszolgáló adott időpontra jellemző állapotának másolata. A visszaállított kiszolgáló hely- és tarifacsomag-értékei ugyanazok, mint a forráskiszolgálón.

Egy szinkron parancsról van szó, amelyet a rendszer a kiszolgáló visszaállítása után visszaad. Ha a visszaállítás kész, keresse meg a létrehozott új kiszolgálót. Ellenőrizze, hogy az adatok a várt módon álltak-e vissza.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az előző lépésekben Azure-erőforrásokat hozott létre egy kiszolgálócsoport számára. Ha nem várható, hogy a jövőben szüksége lesz ezekre az erőforrásokra, törölje a kiszolgálót. A kiszolgálócsoport *Áttekintés* lapján kattintson a *Törlés* gombra. Amikor a rendszer rákérdez egy előugró oldalra, erősítse meg a kiszolgálócsoport nevét, és kattintson a végleges *Törlés* gombra.


## <a name="next-steps"></a>Következő lépések
Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket az Azure CLI (parancssori felület) és más segédprogramok használatával:
> [!div class="checklist"]
> * Azure-adatbázis létrehozása PostgreSQL-kiszolgálóhoz
> * A kiszolgáló tűzfalának konfigurálása
> * Adatbázis létrehozása a **psql** segédprogram használatával
> * Mintaadatok betöltése
> * Adatok lekérdezése
> * Adatok frissítése
> * Adatok visszaállítása

> [!div class="nextstepaction"]
> [Az első Azure Database for PostgreSQL szolgáltatás megtervezése az Azure Portallal](tutorial-design-database-using-azure-portal.md)

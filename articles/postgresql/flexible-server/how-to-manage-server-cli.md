---
title: Kiszolgáló kezelése – Azure CLI – Azure Database for PostgreSQL – rugalmas kiszolgáló
description: Megtudhatja, hogyan kezelheti Azure Database for PostgreSQL-rugalmas kiszolgálót az Azure CLI-vel.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 278f8f816909a7e365d7e45d04c5169950e79a65
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96493678"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-by-using-the-azure-cli"></a>Azure Database for PostgreSQL rugalmas kiszolgáló kezelése az Azure CLI használatával

> [!IMPORTANT]
> Azure Database for PostgreSQL – a rugalmas kiszolgáló előzetes verzióban érhető el.

Ez a cikk bemutatja, hogyan kezelheti az Azure-ban üzembe helyezett rugalmas kiszolgálót. A felügyeleti feladatok közé tartozik a számítási és tárolási skálázás, a rendszergazdai jelszó alaphelyzetbe állítása és a kiszolgáló adatainak megtekintése.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot. 

Az Azure CLI 2,0-es vagy újabb verziójának kell futnia helyileg. A telepített verziók megtekintéséhez futtassa az `az --version` parancsot. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

Jelentkezzen be a fiókjába az az [login](/cli/azure/reference-index#az-login) paranccsal. 

```azurecli-interactive
az login
```

Válassza ki az előfizetését az az [Account set](/cli/azure/account) parancs használatával. Jegyezze fel az **ID** értéket az az **login** kimenetből, hogy az a következő parancs **előfizetés** argumentumának értékeként használja. Ha több előfizetéssel rendelkezik, válassza ki azt az előfizetést, amelyre az erőforrást számlázni kell. Az összes előfizetés azonosításához használja az az [Account List](/cli/azure/account#az-account-list) parancsot.

```azurecli
az account set --subscription <subscription id>
```

> [!Important]
> Ha még nem hozott létre rugalmas kiszolgálót, ezt az útmutató útmutatása szerint kell végrehajtania.

## <a name="scale-compute-and-storage"></a>Számítási és tárolási méretezés

A következő parancs használatával könnyedén méretezheti a számítási szintet, a virtuális mag és a tárterületet. Az összes futtatható kiszolgálói művelet listáját az az [postgres rugalmas-Server](/cli/azure/postgres/flexible-server) áttekintés című cikkben tekintheti meg.

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v3 --storage-size 6144
```

Az előző kódban szereplő argumentumok részletei a következők:

**Beállítás** | **Mintaérték** | **Leírás**
---|---|---
name | mydemoserver | Adjon meg egy egyedi nevet a kiszolgáló számára. A kiszolgálónév csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja. Az értéknek 3 – 63 karaktert kell tartalmaznia.
resource-group | myResourceGroup | Adja meg az Azure-erőforráscsoport nevét.
sku-name|Standard_D4ds_v3|Adja meg a számítási rétegek és a méret nevét. Az érték a következő konvenciót követi: *Standard_ {VM size}* a gyorsírásban. További információkért tekintse meg a [díjszabási szintet](../concepts-pricing-tiers.md) .
storage-size | 6144 | Adja meg a kiszolgáló tárolási kapacitását megabájtban. A minimális érték 5120, ami növeli a 1024-es növekményeket.

> [!IMPORTANT]
> A tárterület nem méretezhető le. 

## <a name="manage-postgresql-databases-on-a-server"></a>PostgreSQL-adatbázisok kezelése kiszolgálón

Számos különféle alkalmazással csatlakozhat a PostgreSQL-kiszolgálóhoz készült Azure-adatbázishoz. Ha az ügyfélszámítógépen telepítve van a PostgreSQL, akkor használhatja a [psql](https://www.postgresql.org/docs/current/static/app-psql.html)helyi példányát. Most a psql parancssori eszköz használatával csatlakozhat a Azure Database for PostgreSQL-kiszolgálóhoz.

1. Futtassa a következő **psql** -parancsot:

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=<dbname>
   ```

   A következő parancs például a **postgres** nevű alapértelmezett adatbázishoz csatlakozik a PostgreSQL-kiszolgáló **mydemoserver.postgres.database.Azure.com** a hozzáférési hitelesítő adatain keresztül. Amikor a rendszer kéri, adja meg a `<server_admin_password>` kiválasztott értéket.
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

   A kapcsolat után a psql eszköz egy **postgres** -üzenetet jelenít meg, ahol megadhatja az SQL-parancsokat. A rendszer figyelmeztetést jelenít meg a kezdeti kapcsolati kimenetben, ha a használt psql verziója eltér a Azure Database for PostgreSQL-kiszolgáló verziójától.

   Példa psql kimenetre:

   ```bash
   psql (11.3, server 12.1)
   WARNING: psql major version 11, server major version 12.
            Some psql features might not work.
   SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
   Type "help" for help.

   postgres=>
   ```

   > [!TIP]
   > Ha a tűzfal nincs konfigurálva az ügyfél IP-címének engedélyezésére, a következő hiba jelenik meg:
   >
   > "psql: végzetes: nincs pg_hba. conf bejegyzés a gazdagéphez `<IP address>` ," myadmin "," postgres "adatbázis" ", SSL on végzetes: SSL-kapcsolat szükséges. Határozza meg az SSL-beállításokat, majd próbálkozzon újra. "
   >
   > Erősítse meg, hogy az ügyfél IP-címe engedélyezett a tűzfalszabályok esetében.

2. Hozzon létre egy **postgresdb** nevű üres adatbázist. Ehhez írja be a következő parancsot a parancssorba:

    ```bash
    CREATE DATABASE postgresdb;
    ```

3. A parancssorban futtassa a következő parancsot a kapcsolatok az újonnan létrehozott adatbázis **postgresdb** való váltásához:

    ```bash
    \c postgresdb
    ```

4. Írja be  `\q` és válassza az ENTER billentyűt a psql kilépéséhez.

Ebben a szakaszban a psql-on keresztül kapcsolódott a Azure Database for PostgreSQL-kiszolgálóhoz, és létrehozott egy üres felhasználói adatbázist.

## <a name="reset-the-admin-password"></a>Rendszergazdai jelszó alaphelyzetbe állítása

A rendszergazdai szerepkör jelszava a következő paranccsal módosítható:

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!IMPORTANT]
> Olyan jelszót válasszon, amely legalább 8 karakterből áll, és legfeljebb 128 karakterből állhat. A jelszónak tartalmaznia kell karaktereket a következő kategóriák közül legalább háromból: 
> - A latin ábécé nagybetűi
> - A latin ábécé kisbetűi
> - Számok
> - Nem alfanumerikus karakterek

## <a name="delete-a-server"></a>Kiszolgáló törlése

A Azure Database for PostgreSQL rugalmas kiszolgáló törléséhez futtassa az az [postgres flexibilis-Server delete](/cli/azure/postgres/flexible-server#az-PostgreSQL-flexible-server-delete) parancsot.

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Következő lépések

- [A biztonsági mentési és visszaállítási fogalmak ismertetése](concepts-backup-restore.md)
- [A kiszolgáló hangolása és figyelése](concepts-monitoring.md)
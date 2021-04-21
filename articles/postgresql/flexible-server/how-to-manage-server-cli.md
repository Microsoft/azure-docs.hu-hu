---
title: Kiszolgáló kezelése – Azure CLI – Azure Database for PostgreSQL – Rugalmas kiszolgáló
description: Megtudhatja, hogyan kezelheti a Azure Database for PostgreSQL – Rugalmas kiszolgálót az Azure CLI-ről.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 9a7e16bf85293a412baf5015af825377438ebb7b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778498"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-by-using-the-azure-cli"></a>Rugalmas Azure Database for PostgreSQL kezelése az Azure CLI használatával

> [!IMPORTANT]
> Azure Database for PostgreSQL – A rugalmas kiszolgáló előzetes verzióban érhető el.

Ez a cikk bemutatja, hogyan kezelheti az Azure-ban üzembe helyezett rugalmas kiszolgálót. A felügyeleti feladatok közé tartozik a számítási és tárolási skálázás, a rendszergazdai jelszó-visszaállítás és a kiszolgáló részleteinek megtekintése.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot. 

Az Azure CLI 2.0-s vagy újabb verziójának helyileg kell futnia. A telepített verziók megtekintéséhez futtassa az `az --version` parancsot. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

Jelentkezzen be a fiókjába az [az login paranccsal.](/cli/azure/reference-index#az_login) 

```azurecli-interactive
az login
```

Válassza ki az előfizetését az [az account set paranccsal.](/cli/azure/account) Jegyezze fel  az az **login** kimenet azonosítóértékét, hogy az az **subscription** argumentum értékeként legyen használva a következő parancsban. Ha több előfizetéssel rendelkezik, válassza ki azt az előfizetést, amelyre az erőforrást ki kell számlázni. Az összes előfizetés azonosításához használja az [az account list](/cli/azure/account#az_account_list) parancsot.

```azurecli
az account set --subscription <subscription id>
```

> [!Important]
> Ha még nem hozott létre rugalmas kiszolgálót, ezt az útmutatót kell követnie.

## <a name="scale-compute-and-storage"></a>Számítás és tárolás skálázható

A számítási réteget, a virtuális magokat és a tárterületet a következő paranccsal skálázhatja fel egyszerűen. Az összes futtatható kiszolgálói művelet listáját az [az postgres flexible-server overview (az az postgres flexible-server overview) (az az postgres flexible-server overview (az az postgres flexible-server overview) (az az postgres flexible-server overview (az az postgres flexible-server áttekintése) oldalon](/cli/azure/postgres/flexible-server) olvashatja el.

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v3 --storage-size 6144
```

Az előző kód argumentumai a következők:

**Beállítás** | **Mintaérték** | **Leírás**
---|---|---
name | mydemoserver | Adjon egyedi nevet a kiszolgálónak. A kiszolgálónév csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja. 3–63 karakter hosszúságú lehet.
resource-group | myResourceGroup | Adja meg az Azure-erőforráscsoport nevét.
sku-name|Standard_D4ds_v3|Adja meg a számítási szint nevét és méretét. Az érték röviden *Standard_{vm size}* konvenciót követi. További [információért tekintse meg a](../concepts-pricing-tiers.md) tarifacsomagokat.
storage-size | 6144 | Adja meg a kiszolgáló tárolási kapacitását megabájtban. A minimális érték 5120, ami 1024-es növekményekben növekszik.

> [!IMPORTANT]
> A tárterület nem skálázható le. 

## <a name="manage-postgresql-databases-on-a-server"></a>PostgreSQL-adatbázisok kezelése kiszolgálón

Számos különféle alkalmazással csatlakozhat a PostgreSQL-kiszolgálóhoz készült Azure-adatbázishoz. Ha az ügyfélszámítógépen telepítve van a PostgreSQL, használhatja a psql helyi [példányát.](https://www.postgresql.org/docs/current/static/app-psql.html) Most a psql parancssori eszközzel csatlakozzunk a Azure Database for PostgreSQL kiszolgálóhoz.

1. Futtassa a **következő psql-parancsot:**

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=<dbname>
   ```

   A következő parancs például a **postgres** nevű alapértelmezett adatbázishoz csatlakozik a PostgreSQL-kiszolgálón, **mydemoserver.postgres.database.azure.com** hitelesítő adataival. Amikor a rendszer kéri, adja meg `<server_admin_password>` a választott et.
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

   A csatlakozás után a psql eszköz egy **postgres-parancssort** jelenít meg, amelyben SQL-parancsokat lehet megadni. A kezdeti kapcsolat kimenetében figyelmeztetés jelenik meg, ha a használt psql-verzió eltér a Azure Database for PostgreSQL verziójától.

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
   > "psql: FATAL: no pg_hba.conf entry for host `<IP address>` , user "myadmin", database "postgres", SSL on FATAL: SSL connection is required. Adja meg az SSL-beállításokat, és próbálja újra."
   >
   > Ellenőrizze, hogy az ügyfél IP-címe engedélyezve van-e a tűzfalszabályok között.

2. Hozzon létre egy **postgresdb** nevű üres adatbázist a következő parancs parancssorba való beírásával:

    ```bash
    CREATE DATABASE postgresdb;
    ```

3. A parancssorban futtassa a következő parancsot az újonnan létrehozott **postgresdb** adatbázishoz való kapcsolódáshoz:

    ```bash
    \c postgresdb
    ```

4. Írja  `\q` be a parancsot, majd válassza az Enter billentyűt a psql-lel való kilépéshez.

Ebben a szakaszban psql-en keresztül csatlakozott a Azure Database for PostgreSQL-kiszolgálóhoz, és létrehozott egy üres felhasználói adatbázist.

## <a name="reset-the-admin-password"></a>Rendszergazdai jelszó visszaállítása

A rendszergazdai szerepkör jelszavát a következő paranccsal módosíthatja:

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!IMPORTANT]
> Olyan jelszót válasszon, amely legalább 8 karakterből és legfeljebb 128 karakterből állhat. A jelszónak tartalmaznia kell karaktereket a következő kategóriák közül háromból: 
> - A latin ábécé nagybetűi
> - A latin ábécé kisbetűi
> - Számok
> - Nem alfanumerikus karakterek

## <a name="delete-a-server"></a>Kiszolgáló törlése

A rugalmas Azure Database for PostgreSQL törléséhez futtassa [az az postgres flexible-server delete](/cli/azure/postgres/flexible-server#az_postgresql_flexible_server_delete) parancsot.

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Következő lépések

- [A biztonsági mentéssel és visszaállításokkal kapcsolatos fogalmak](concepts-backup-restore.md)
- [A kiszolgáló hangolása és figyelése](concepts-monitoring.md)
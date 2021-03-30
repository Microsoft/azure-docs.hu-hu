---
title: Biztonsági mentés és visszaállítás – Azure CLI-Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Megtudhatja, hogyan állíthatja be a biztonsági mentési konfigurációkat, és hogyan állíthatja vissza a kiszolgálót Azure Database for PostgreSQL egyetlen kiszolgálón az Azure CLI használatával.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 10/25/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: ef397eb67c1f60c14fb36bf455236d84b730f611
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94659573"
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Kiszolgáló biztonsági mentése és visszaállítása Azure Database for PostgreSQL – egyetlen kiszolgálón az Azure CLI használatával

A visszaállítási funkciók engedélyezéséhez rendszeresen biztonsági mentést kell készíteni Azure Database for PostgreSQL-kiszolgálókról. A szolgáltatás használatával visszaállíthatja a kiszolgálót és az összes adatbázisát egy korábbi időpontra, egy új kiszolgálón.

## <a name="prerequisites"></a>Előfeltételek
A útmutató lépéseinek elvégzéséhez:

- Szüksége van egy [Azure Database for PostgreSQL kiszolgálóra és adatbázisra](quickstart-create-server-database-azure-cli.md).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

 - Ehhez a cikkhez az Azure CLI 2,0-es vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="set-backup-configuration"></a>Biztonsági mentési konfiguráció beállítása

Választhatja a kiszolgáló helyi redundáns biztonsági mentések vagy földrajzilag redundáns biztonsági másolatok konfigurálását a kiszolgáló létrehozásakor. 

> [!NOTE]
> A kiszolgáló létrehozása után a redundancia, földrajzilag redundáns vagy helyileg redundáns, nem állítható be.
>

A-kiszolgáló parancson keresztüli létrehozásakor `az postgres server create` a `--geo-redundant-backup` paraméter határozza meg a biztonsági mentési redundancia beállítást. Ha `Enabled` a, Geo redundáns biztonsági mentések készülnek. Vagy ha `Disabled` a rendszer helyileg redundáns biztonsági mentést végez. 

A biztonsági mentés megőrzési időtartamát a paraméter határozza meg `--backup-retention-days` . 

További információ ezekről az értékekről a létrehozás során: [Azure Database for PostgreSQL Server CLI](quickstart-create-server-database-azure-cli.md)rövid útmutató.

A kiszolgáló biztonsági mentési megőrzési időszaka a következőképpen módosítható:

```azurecli-interactive
az postgres server update --name mydemoserver --resource-group myresourcegroup --backup-retention 10
```

Az előző példa a mydemoserver biztonsági mentés megőrzési időtartamát 10 napra módosítja.

A biztonsági másolatok megőrzési időszaka azt szabályozza, hogy az adott időpontra visszamenőleges visszaállítás hogyan kérhető le, mert az elérhető biztonsági másolatokon alapul. Az időponthoz való visszaállítás a következő szakaszban olvasható.

## <a name="server-point-in-time-restore"></a>Kiszolgáló időpontjának visszaállítása
A kiszolgálót visszaállíthatja egy korábbi időpontra. A visszaállított adatfájlokat egy új kiszolgálóra másolja a rendszer, és a meglévő kiszolgáló a következő marad:. Ha például egy táblát a mai napig véletlenül dobják el, a visszaállíthatja azt az időpontot megelőzően. Ezután lekérheti a hiányzó táblát és az adatait a kiszolgáló visszaállított példányáról. 

A-kiszolgáló visszaállításához használja az Azure CLI az [postgres Server Restore](/cli/azure/postgres/server) parancsot.

### <a name="run-the-restore-command"></a>A Restore parancs futtatása

A kiszolgáló visszaállításához az Azure CLI parancssorában adja meg a következő parancsot:

```azurecli-interactive
az postgres server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2018-03-13T13:59:00Z --source-server mydemoserver
```

A `az postgres server restore` parancshoz a következő paraméterek szükségesek:

| Beállítás | Ajánlott érték | Leírás  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  Az erőforráscsoport, amelyben a forráskiszolgáló található.  |
| name | mydemoserver-restored | A visszaállítási paranccsal létrehozott új kiszolgáló neve. |
| restore-point-in-time | 2018-03-13T13:59:00Z | Válasszon ki egy időpontot a visszaállításhoz. Ennek a dátumnak és időnek a forráskiszolgáló biztonsági mentésének megőrzési időszakán belül kell lennie. Használja a ISO8601 dátum és idő formátumát. Használhatja például a saját helyi időzónáját, például: `2018-03-13T05:59:00-08:00` . Az UTC Zulu formátumot is használhatja, például: `2018-03-13T13:59:00Z` . |
| source-server | mydemoserver | A forráskiszolgáló neve vagy azonosítója, amelyről a visszaállítást végzi. |

Amikor egy kiszolgálót egy korábbi időpontra állít vissza, létrejön egy új kiszolgáló. A rendszer átmásolja az eredeti kiszolgálót és a megadott időponthoz tartozó adatbázisait az új kiszolgálóra.

A visszaállított kiszolgáló helye és árképzési szintjei változatlanok maradnak az eredeti kiszolgálóval. 

A visszaállítási folyamat befejeződése után keresse meg az új kiszolgálót, és győződjön meg róla, hogy az Adathelyreállítás a várt módon történik. Az új kiszolgáló ugyanazzal a kiszolgáló-rendszergazdai bejelentkezési névvel és jelszóval rendelkezik, amely a visszaállítás megkezdésének időpontjában érvényes a meglévő kiszolgálóhoz. A jelszót az új kiszolgáló **áttekintő** oldaláról lehet megváltoztatni.

A visszaállítás során létrehozott új kiszolgáló nem rendelkezik az eredeti kiszolgálón található tűzfalszabályokkal vagy VNet-szolgáltatásvégpontokkal. Ezeket a szabályokat külön kell beállítani ehhez az új kiszolgálóhoz.

## <a name="geo-restore"></a>Geo-visszaállítás
Ha a kiszolgálót földrajzilag redundáns biztonsági mentésre konfigurálta, akkor a rendszer létrehoz egy új kiszolgálót a meglévő kiszolgáló biztonsági másolatából. Ezt az új kiszolgálót bármely olyan régióban létre lehet hozni, amely Azure Database for PostgreSQL elérhető.  

Ha egy olyan kiszolgálót szeretne létrehozni, amely egy földrajzi redundáns biztonsági mentést használ, használja az Azure CLI- `az postgres server georestore` parancsot.

> [!NOTE]
> A kiszolgálók első létrehozásakor előfordulhat, hogy a Geo-visszaállításhoz nem lesz azonnal elérhető. A szükséges metaadatok feltöltése néhány órát is igénybe vehet.
>

A kiszolgáló geo-visszaállításához az Azure CLI parancssorában adja meg a következő parancsot:

```azurecli-interactive
az postgres server georestore --resource-group myresourcegroup --name mydemoserver-georestored --source-server mydemoserver --location eastus --sku-name GP_Gen4_8 
```
Ez a parancs létrehoz egy új, *mydemoserver-georestored* nevű kiszolgálót az USA keleti régiójában, amely a *myresourcegroup*-hoz fog tartozni. Ez egy általános célú, Gen 4 kiszolgáló 8 virtuális mag. A kiszolgáló a *mydemoserver* a Geo-redundáns biztonsági másolatból jön létre, amely szintén az erőforráscsoport *myresourcegroup*

Ha az új kiszolgálót egy másik erőforráscsoporthoz szeretné létrehozni a meglévő kiszolgálóról, akkor a (z) `--source-server` paraméterben az alábbi példában látható módon kell minősítenie a kiszolgálónevet:

```azurecli-interactive
az postgres server georestore --resource-group newresourcegroup --name mydemoserver-georestored --source-server "/subscriptions/$<subscription ID>/resourceGroups/$<resource group ID>/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver" --location eastus --sku-name GP_Gen4_8

```

A `az postgres server georestore` parancshoz a következő paraméterek szükségesek:

| Beállítás | Ajánlott érték | Leírás  |
| --- | --- | --- |
|resource-group| myResourceGroup | Annak az erőforráscsoportnak a neve, amelyhez az új kiszolgáló tartozni fog.|
|name | mydemoserver – georestored | Az új kiszolgáló neve. |
|source-server | mydemoserver | Annak a meglévő kiszolgálónak a neve, amelynek a földrajzi redundáns biztonsági másolatait használja a rendszer. |
|location | eastus | Az új kiszolgáló helye. |
|sku-name| GP_Gen4_8 | Ez a paraméter beállítja az árképzési szintet, a számítási generációt és az új kiszolgáló virtuális mag számát. GP_Gen4_8 térképeket egy általános célú, Gen 4 Server 8 virtuális mag.|

Amikor új kiszolgálót hoz létre a Geo-visszaállítással, az örökli a forráskiszolgáló azonos tárolási méretét és díjszabási szintjét. Ezek az értékek nem módosíthatók a létrehozás során. Az új kiszolgáló létrehozása után a tárolási mérete méretezhető.

A visszaállítási folyamat befejeződése után keresse meg az új kiszolgálót, és győződjön meg róla, hogy az Adathelyreállítás a várt módon történik. Az új kiszolgáló ugyanazzal a kiszolgáló-rendszergazdai bejelentkezési névvel és jelszóval rendelkezik, amely a visszaállítás megkezdésének időpontjában érvényes a meglévő kiszolgálóhoz. A jelszót az új kiszolgáló **áttekintő** oldaláról lehet megváltoztatni.

A visszaállítás során létrehozott új kiszolgáló nem rendelkezik az eredeti kiszolgálón található tűzfalszabályokkal vagy VNet-szolgáltatásvégpontokkal. Ezeket a szabályokat külön kell beállítani ehhez az új kiszolgálóhoz.

## <a name="next-steps"></a>Következő lépések
- További információ a szolgáltatás [biztonsági mentéséről](concepts-backup.md)
- A [replikák](concepts-read-replicas.md) megismerése
- További információ az [üzletmenet-folytonossági](concepts-business-continuity.md) lehetőségekről

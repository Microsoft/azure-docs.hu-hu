---
title: Biztonsági mentés és visszaállítás – Azure CLI – Azure Database for MySQL
description: Megtudhatja, hogyan készít biztonsági mentést és visszaállítást egy Azure Database for MySQL az Azure CLI használatával.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/27/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8c8b0f37729ea20a62838d736dbed59f05c584c6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780406"
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-mysql-using-the-azure-cli"></a>Kiszolgáló biztonsági mentésének és visszaállításának Azure Database for MySQL az Azure CLI használatával

Azure Database for MySQL biztonsági másolat rendszeresen biztonsági mentést végez a visszaállítási szolgáltatások engedélyezéséhez. Ezzel a funkcióval visszaállíthatja a kiszolgálót és annak összes adatbázisát egy korábbi időpontra egy új kiszolgálón.

## <a name="prerequisites"></a>Előfeltételek

Az útmutató befejezéséhez:

- Szüksége lesz egy [Azure Database for MySQL-kiszolgálóra és -adatbázisra.](quickstart-create-mysql-server-database-using-azure-cli.md)

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Ehhez a cikkhez az Azure CLI 2.0-s vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="set-backup-configuration"></a>Biztonsági mentési konfiguráció beállítása

A kiszolgáló helyileg redundáns vagy földrajzilag redundáns biztonsági mentésre való konfigurálása között a kiszolgáló létrehozásakor kell választania. 

> [!NOTE]
> A kiszolgáló létrehozása után a földrajzilag redundáns és helyileg redundáns redundancia nem kapcsolható át.
>

Amikor a paranccsal hoz létre egy kiszolgálót, a paraméter dönti el `az mysql server create` a Biztonsági mentés `--geo-redundant-backup` redundancia beállítását. Ha `Enabled` a rendszer georedundáns biztonsági mentéseket készít. Vagy ha `Disabled` helyileg redundáns biztonsági mentést készít. 

A biztonsági másolat megőrzési ideje a paraméterrel van `--backup-retention` beállítva. 

Az értékek létrehozás során való beállításával kapcsolatos további információkért tekintse meg a [Azure Database for MySQL-kiszolgálói CLI rövid útmutatóját.](quickstart-create-mysql-server-database-using-azure-cli.md)

A biztonsági másolatok megőrzési ideje a következőképpen módosítható:

```azurecli-interactive
az mysql server update --name mydemoserver --resource-group myresourcegroup --backup-retention 10
```

Az előző példa a mydemoserver biztonsági másolat megőrzési időszakát 10 napra módosítja.

A biztonsági másolatok megőrzési ideje azt határozza meg, hogy milyen hosszú az időponthoz tartozó visszaállítás lekérése, mivel az az elérhető biztonsági másolatok alapján van. Az időponthoz időben való visszaállításról a következő szakaszban lesz bővebben is leírás.

## <a name="server-point-in-time-restore"></a>Kiszolgáló időponthoz időben való visszaállítása
Visszaállíthatja a kiszolgálót egy korábbi időpontra. A visszaállított adatokat a rendszer egy új kiszolgálóra másolja, és a meglévő kiszolgálót a rendszer a jelenlegi ként hagyja. Ha például egy tábla véletlenül ma délben van eldobva, visszaállíthatja a táblát a dél előtti időpontra. Ezután lekérheti a hiányzó táblát és adatokat a kiszolgáló visszaállított példányából. 

A kiszolgáló visszaállításához használja az Azure CLI [az mysql server restore parancsot.](/cli/azure/mysql/server#az_mysql_server_restore)

### <a name="run-the-restore-command"></a>A visszaállítási parancs futtatása

A kiszolgáló visszaállításához írja be a következő parancsot az Azure CLI parancssorba:

```azurecli-interactive
az mysql server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2018-03-13T13:59:00Z --source-server mydemoserver
```

A `az mysql server restore` parancshoz a következő paraméterekre van szükség:

| Beállítás | Ajánlott érték | Leírás  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  Az az erőforráscsoport, amelyben a forráskiszolgáló található.  |
| name | mydemoserver-restored | A visszaállítási paranccsal létrehozott új kiszolgáló neve. |
| restore-point-in-time | 2018-03-13T13:59:00Z | Válassza ki azt az időpontot, amelybe vissza kell állítania. Ennek a dátumnak és időnek a forráskiszolgáló biztonsági mentésének megőrzési időszakán belül kell lennie. Használja az ISO8601 dátum- és időformátumot. Használhatja például a saját helyi időzónát, `2018-03-13T05:59:00-08:00` például: . Használhatja az UTC Zulu formátumot is, `2018-03-13T13:59:00Z` például: . |
| source-server | mydemoserver | A forráskiszolgáló neve vagy azonosítója, amelyről a visszaállítást végzi. |

Amikor visszaállít egy kiszolgálót egy korábbi időpontra, a rendszer létrehoz egy új kiszolgálót. A rendszer átmásolja az eredeti kiszolgálót és annak adatbázisát a megadott időpontból az új kiszolgálóra.

A visszaállított kiszolgáló hely- és tarifacsomag-értékei ugyanazok maradnak, mint az eredeti kiszolgáló. 

A visszaállítási folyamat befejezése után keresse meg az új kiszolgálót, és ellenőrizze, hogy az adatok a várt módon vannak-e visszaállva. Az új kiszolgáló ugyanazokkal a kiszolgáló-rendszergazdai bejelentkezési névvel és jelszóval rendelkezik, amely érvényes volt a meglévő kiszolgálóra a visszaállítás elindításakor. A jelszó az új kiszolgáló Áttekintés oldalának **használatával módosítható.**

Emellett a visszaállítási művelet befejezése után két kiszolgálóparaméter áll vissza az alapértelmezett értékekre (és a rendszer nem másol át az elsődleges kiszolgálóról) a visszaállítási művelet után
*   time_zone – Ez az érték a DEFAULT (ALAPÉRTELMEZETT) SYSTEM értékre **van állítva**
*   event_scheduler – A event_scheduler ki van **kapcsolva** a visszaállított kiszolgálón

Át kell másolnia az értéket az elsődleges kiszolgálóról, és be kell állítania a visszaállított kiszolgálón a kiszolgálóparaméter [újrakonfigurálásának segítségével.](howto-server-parameters.md)

A visszaállítás során létrehozott új kiszolgáló nem rendelkezik az eredeti kiszolgálón meglévő VNet-szolgáltatásvégpontokkal. Ezeket a szabályokat külön kell beállítani ehhez az új kiszolgálóhoz. A rendszer visszaállítja az eredeti kiszolgáló tűzfalszabályát.

## <a name="geo-restore"></a>Georedens visszaállítás
Ha a kiszolgálót földrajzilag redundáns biztonsági mentésre konfigurálta, új kiszolgálót is létre lehet hozva a meglévő kiszolgáló biztonsági másolatából. Ez az új kiszolgáló bármely olyan régióban létre Azure Database for MySQL elérhető.  

Georedundáns biztonsági mentést használó kiszolgáló létrehozásához használja az Azure `az mysql server georestore` CLI-parancsot.

> [!NOTE]
> A kiszolgáló első létrehozásakor előfordulhat, hogy az nem lesz azonnal elérhető a georedens visszaállításhoz. A szükséges metaadatok feltöltése néhány órát is igénybe vehet.
>

A kiszolgáló földrajzi visszaállításához írja be a következő parancsot az Azure CLI parancssorba:

```azurecli-interactive
az mysql server georestore --resource-group myresourcegroup --name mydemoserver-georestored --source-server mydemoserver --location eastus --sku-name GP_Gen5_8 
```
Ez a parancs létrehoz egy *mydemoserver-georestored* nevű új kiszolgálót az USA keleti részén, amely a *myresourcegroup csoporthoz fog tartozni.* Ez egy általános célú, 5. generációs kiszolgáló 8 virtuális maggal. A kiszolgáló a *mydemoserver georedundáns* biztonsági másolatából jön létre, amely szintén a *myresourcegroup erőforráscsoportban található*

Ha az új kiszolgálót a meglévő kiszolgálótól eltérő erőforráscsoportban szeretné létrehozni, akkor a paraméterben a kiszolgáló nevét az alábbi példának `--source-server` megfelelőnek kell minősítenie:

```azurecli-interactive
az mysql server georestore --resource-group newresourcegroup --name mydemoserver-georestored --source-server "/subscriptions/$<subscription ID>/resourceGroups/$<resource group ID>/providers/Microsoft.DBforMySQL/servers/mydemoserver" --location eastus --sku-name GP_Gen5_8

```

A `az mysql server georestore` parancshoz a következő paraméterekre van szükség:

| Beállítás | Ajánlott érték | Leírás  |
| --- | --- | --- |
|resource-group| myResourceGroup | Annak az erőforráscsoportnak a neve, amelyhez az új kiszolgáló tartozni fog.|
|name | mydemoserver-georestored | Az új kiszolgáló neve. |
|source-server | mydemoserver | Annak a meglévő kiszolgálónak a neve, amelynek georedundáns biztonsági másolatai vannak használva. |
|location | eastus | Az új kiszolgáló helye. |
|sku-name| GP_Gen5_8 | Ez a paraméter beállítja az új kiszolgáló tarifacsomagját, számítási generációját és virtuális magszámát. GP_Gen5_8 egy 8 virtuális maggal általános célú 5. generációs kiszolgálóra van leképezve.|

Amikor georedúnár visszaállítással hoz létre új kiszolgálót, az ugyanazt a tárterületméretet és tarifacsomagot örökli, mint a forráskiszolgáló. Ezek az értékek létrehozáskor nem módosíthatók. Az új kiszolgáló létrehozása után a tárterület mérete skálázható fel.

A visszaállítási folyamat befejezése után keresse meg az új kiszolgálót, és ellenőrizze, hogy az adatok a várt módon vannak-e visszaállva. Az új kiszolgáló ugyanazokkal a kiszolgáló-rendszergazdai bejelentkezési névvel és jelszóval rendelkezik, amely a meglévő kiszolgálóra érvényes volt a visszaállítás elindításakor. A jelszó az új kiszolgáló Áttekintés oldalára **módosítható.**

A visszaállítás során létrehozott új kiszolgáló nem rendelkezik az eredeti kiszolgálón meglévő VNet-szolgáltatásvégpontokkal. Ezeket a szabályokat külön kell beállítani ehhez az új kiszolgálóhoz. A rendszer visszaállítja az eredeti kiszolgáló tűzfalszabályát.

## <a name="next-steps"></a>Következő lépések
- További információ a szolgáltatás biztonsági [másolatairól](concepts-backup.md)
- További tudnivalók [a replikákról](concepts-read-replicas.md)
- További információ az [üzletmenet-folytonossági lehetőségekről](concepts-business-continuity.md)

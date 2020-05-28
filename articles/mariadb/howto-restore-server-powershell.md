---
title: Biztonsági mentés és visszaállítás – Azure PowerShell – Azure Database for MariaDB
description: Megtudhatja, hogyan készíthet biztonsági másolatot és állíthat vissza egy kiszolgálót a Azure Database for MariaDB Azure PowerShell használatával.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurepowershell
ms.topic: conceptual
ms.date: 05/26/2020
ms.openlocfilehash: c16c18a7056b2d23b64caeca4a88905b3dd2557c
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84041944"
---
# <a name="how-to-back-up-and-restore-an-azure-database-for-mariadb-server-using-powershell"></a>Azure Database for MariaDB-kiszolgáló biztonsági mentése és visszaállítása a PowerShell használatával

A visszaállítási funkciók engedélyezéséhez a rendszer rendszeresen biztonsági mentést készít Azure Database for MariaDB kiszolgálóról. A szolgáltatás használatával visszaállíthatja a kiszolgálót és az összes adatbázisát egy korábbi időpontra, egy új kiszolgálón.

## <a name="prerequisites"></a>Előfeltételek

A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:

- Az az [PowerShell-modul](https://docs.microsoft.com/powershell/azure/install-az-ps) helyileg vagy [Azure Cloud Shell](https://shell.azure.com/) telepítve a böngészőben
- Egy [Azure Database for MariaDB-kiszolgáló](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Az az. MariaDb PowerShell-modul előzetes verzióban érhető el, és a következő paranccsal külön kell telepítenie az az PowerShell-modulból: `Install-Module -Name Az.MariaDb -AllowPrerelease` .
> Amint az az. MariaDb PowerShell-modul általánosan elérhetővé válik, az a PowerShell modul kiadásainak része lesz, és natív módon elérhető a Azure Cloud Shellon belülről.

Ha a PowerShell helyi használatát választja, kapcsolódjon az Azure-fiókjához a [AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) parancsmag használatával.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-backup-configuration"></a>Biztonsági mentési konfiguráció beállítása

A kiszolgáló létrehozásakor választhat, hogy a kiszolgálót helyileg redundáns vagy földrajzilag redundáns biztonsági másolatokként konfigurálja-e.

> [!NOTE]
> A kiszolgáló létrehozása után a redundancia nem módosítható, földrajzilag redundáns, a helyileg redundáns.

A kiszolgáló parancson keresztüli létrehozása közben `New-AzMariaDbServer` a **GeoRedundantBackup** paraméter határozza meg a biztonsági mentési redundancia beállítást. Ha **engedélyezve**van, a rendszer redundáns biztonsági mentéseket végez. Ha **le van tiltva**, a rendszer helyileg redundáns biztonsági mentéseket végez.

A biztonsági mentés megőrzési időtartamát a **BackupRetentionDay** paraméter állítja be.

Az értékek kiszolgáló létrehozásakor történő beállításával kapcsolatos további információkért lásd: [Azure Database for MariaDB kiszolgáló létrehozása a PowerShell használatával](quickstart-create-mariadb-server-database-using-azure-powershell.md).

A kiszolgáló biztonsági mentési megőrzési időszaka a következőképpen módosítható:

```azurepowershell-interactive
Update-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -BackupRetentionDay 10
```

Az előző példa a mydemoserver biztonsági mentés megőrzési időtartamát 10 napra módosítja.

A biztonsági másolatok megőrzési időszaka azt szabályozza, hogy az adott időpontra visszamenőleges visszaállítás hogyan állítható le, mivel az elérhető biztonsági másolatokon alapul. Az időponthoz való visszaállítás a következő szakaszban olvasható.

## <a name="server-point-in-time-restore"></a>Kiszolgáló időpontjának visszaállítása

A kiszolgálót visszaállíthatja egy korábbi időpontra. A visszaállított adatfájlokat egy új kiszolgálóra másolja a rendszer, és a meglévő kiszolgáló változatlan marad. Ha például egy tábla véletlenül el van dobva, visszaállíthatja azt az időpontra, ameddig csak a drop történt. Ezután lekérheti a hiányzó táblát és az adatait a kiszolgáló visszaállított példányáról.

A kiszolgáló visszaállításához használja a `Restore-AzMariaDbServer` PowerShell-parancsmagot.

### <a name="run-the-restore-command"></a>A Restore parancs futtatása

A kiszolgáló visszaállításához futtassa a következő példát a PowerShellből.

```azurepowershell-interactive
$restorePointInTime = (Get-Date).AddMinutes(-10)
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMariaDbServer -Name mydemoserver-restored -ResourceGroupName myresourcegroup -RestorePointInTime $restorePointInTime -UsePointInTimeRestore
```

A parancsmag **PointInTimeRestore** paraméterének `Restore-AzMariaDbServer` a következő paramétereket kell megadnia:

| Beállítás | Ajánlott érték | Leírás  |
| --- | --- | --- |
| ResourceGroupName |  myResourceGroup |  Az erőforráscsoport, amelyben a forráskiszolgáló található.  |
| Name | mydemoserver-restored | A visszaállítási paranccsal létrehozott új kiszolgáló neve. |
| RestorePointInTime | 2020 – 03-13T13:59:00Z | Válasszon ki egy időpontot a visszaállításhoz. Ennek a dátumnak és időnek a forráskiszolgáló biztonsági mentésének megőrzési időszakán belül kell lennie. Használja a ISO8601 dátum és idő formátumát. Használhatja például a saját helyi időzónáját, például **2020-03-13T05:59:00-08:00**. Használhatja az UTC Zulu formátumot is, például **2018-03-13T13:59:00Z**. |
| UsePointInTimeRestore | `<SwitchParameter>` | A visszaállításhoz használja az időponthoz tartozó módot. |

Amikor egy kiszolgálót egy korábbi időpontra állít vissza, létrejön egy új kiszolgáló. Az eredeti kiszolgáló és a megadott időponthoz tartozó adatbázisai az új kiszolgálóra lesznek másolva.

A visszaállított kiszolgáló helye és árképzési szintjei változatlanok maradnak az eredeti kiszolgálóval.

A visszaállítási folyamat befejeződése után keresse meg az új kiszolgálót, és győződjön meg róla, hogy az Adathelyreállítás a várt módon történik. Az új kiszolgáló ugyanazzal a kiszolgáló-rendszergazdai bejelentkezési névvel és jelszóval rendelkezik, amely a visszaállítás elindításának időpontjában érvényes a meglévő kiszolgálóhoz. A jelszót az új kiszolgáló **áttekintő** oldaláról lehet megváltoztatni.

A visszaállítás során létrehozott új kiszolgáló nem rendelkezik az eredeti kiszolgálón található VNet-szolgáltatási végpontokkal. Ezeket a szabályokat külön kell beállítani az új kiszolgálóhoz. A rendszer visszaállítja az eredeti kiszolgáló tűzfalszabályok beállításait.

## <a name="geo-restore"></a>Geo-visszaállítás

Ha a kiszolgálót földrajzilag redundáns biztonsági mentésre konfigurálta, akkor a rendszer létrehoz egy új kiszolgálót a meglévő kiszolgáló biztonsági másolatából. Ezt az új kiszolgálót bármely olyan régióban létre lehet hozni, amely Azure Database for MariaDB elérhető.

Ha olyan kiszolgálót szeretne létrehozni, amely egy földrajzi redundáns biztonsági mentést használ, használja a `Restore-AzMariaDbServer` parancsot a **UseGeoRestore** paraméterrel.

> [!NOTE]
> A kiszolgálók első létrehozásakor előfordulhat, hogy a Geo-visszaállításhoz nem lesz azonnal elérhető. A szükséges metaadatok feltöltése néhány órát is igénybe vehet.

A kiszolgáló geo-visszaállításához futtassa a következő példát a PowerShellből:

```azurepowershell-interactive
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMariaDbServer -Name mydemoserver-georestored -ResourceGroupName myresourcegroup -Location eastus -Sku GP_Gen5_8 -UseGeoRestore
```

Ez a példa egy **mydemoserver-georestored** nevű új kiszolgálót hoz létre az USA keleti régiójában, amely a következőhöz tartozik: **myresourcegroup**. Ez egy általános célú, Gen 5 kiszolgáló 8 virtuális mag. A kiszolgáló a **mydemoserver**a Geo-redundáns biztonsági másolatból jön létre, az erőforráscsoport **myresourcegroup**is.

Ha az új kiszolgálót egy másik erőforráscsoporthoz szeretné létrehozni a meglévő kiszolgálóról, az alábbi példában látható módon adja meg az új erőforráscsoport-nevet a **ResourceGroupName** paraméter használatával:

```azurepowershell-interactive
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMariaDbServer -Name mydemoserver-georestored -ResourceGroupName newresourcegroup -Location eastus -Sku GP_Gen5_8 -UseGeoRestore
```

A parancsmag **GeoRestore** paraméterének `Restore-AzMariaDbServer` a következő paramétereket kell megadnia:

| Beállítás | Ajánlott érték | Leírás  |
| --- | --- | --- |
|ResourceGroupName | myResourceGroup | Azon erőforráscsoport neve, amelyhez az új kiszolgáló tartozik.|
|Name | mydemoserver – georestored | Az új kiszolgáló neve. |
|Hely | eastus | Az új kiszolgáló helye. |
|UseGeoRestore | `<SwitchParameter>` | A visszaállításhoz használja a Geo üzemmódot. |

Amikor új kiszolgálót hoz létre a Geo-visszaállítás használatával, az örökli a forrás-kiszolgálóval megegyező tárolási méretet és díjszabási szintet, kivéve, ha az **SKU** paraméter meg van adva.

A visszaállítási folyamat befejeződése után keresse meg az új kiszolgálót, és győződjön meg róla, hogy az Adathelyreállítás a várt módon történik. Az új kiszolgáló ugyanazzal a kiszolgáló-rendszergazdai bejelentkezési névvel és jelszóval rendelkezik, amely a visszaállítás elindításának időpontjában érvényes a meglévő kiszolgálóhoz. A jelszót az új kiszolgáló **áttekintő** oldaláról lehet megváltoztatni.

A visszaállítás során létrehozott új kiszolgáló nem rendelkezik az eredeti kiszolgálón található VNet-szolgáltatási végpontokkal. Ezeket a szabályokat külön kell beállítani az új kiszolgálóhoz. A rendszer visszaállítja az eredeti kiszolgáló tűzfalszabályok beállításait.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Azure Database for MariaDB kiszolgáló paramétereinek testreszabása a PowerShell használatával](howto-configure-server-parameters-using-powershell.md)

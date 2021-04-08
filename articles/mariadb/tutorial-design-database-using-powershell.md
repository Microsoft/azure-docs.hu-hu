---
title: 'Oktatóanyag: kiszolgáló megtervezése – Azure PowerShell – Azure Database for MariaDB'
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre és kezelhet Azure Database for MariaDB-kiszolgálókat és-adatbázisokat a PowerShell használatával.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurepowershell
ms.topic: tutorial
ms.date: 05/26/2020
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 9b4500df459e4d4ef67f97dc4fa923988f30401b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664501"
---
# <a name="tutorial-design-an-azure-database-for-mariadb-using-powershell"></a>Oktatóanyag: Azure Database for MariaDB tervezése a PowerShell használatával

A Azure Database for MariaDB egy, a Microsoft Cloud-on alapuló, a MariaDB Community Edition adatbázismotor alapján működő adatbázis-szolgáltatás. Ebben az oktatóanyagban a PowerShell és más segédprogramok segítségével a következőket sajátíthatja el:

> [!div class="checklist"]
> - Azure Database for MariaDB létrehozása
> - A kiszolgáló tűzfalának konfigurálása
> - Adatbázis létrehozása a [MySQL parancssori eszköz](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) használatával
> - Mintaadatok betöltése
> - Adatok lekérdezése
> - Adatok frissítése
> - Adatok visszaállítása

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

Ha a PowerShell helyi használatát választja, akkor ehhez a cikkhez telepítenie kell az az PowerShell-modult, és csatlakoznia kell az Azure-fiókjához a [AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmag használatával. Az az PowerShell-modul telepítésével kapcsolatos további információkért lásd: [Install Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Az az. MariaDb PowerShell-modul előzetes verzióban érhető el, és a következő paranccsal külön kell telepítenie az az PowerShell-modulból: `Install-Module -Name Az.MariaDb -AllowPrerelease` .
> Amint az az. MariaDb PowerShell-modul általánosan elérhetővé válik, az a PowerShell modul kiadásainak része lesz, és natív módon elérhető a Azure Cloud Shellon belülről.

Ha első alkalommal használja a Azure Database for MariaDB szolgáltatást, regisztrálnia kell a **Microsoft. DBforMariaDB** erőforrás-szolgáltatót.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.DBforMariaDB
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Ha több Azure-előfizetéssel rendelkezik, válassza ki a megfelelő előfizetést, amelyben az erőforrásokat számlázni kell. Válasszon egy adott előfizetés-azonosítót a [set-AzContext](/powershell/module/az.accounts/set-azcontext) parancsmag használatával.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy [Azure-erőforráscsoportot](../azure-resource-manager/management/overview.md) a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancsmag használatával. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer csoportként helyezi üzembe és kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy **myresourcegroup** nevű ERŐFORRÁSCSOPORTOT az **USA nyugati** régiójában.

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroup -Location westus
```

## <a name="create-an-azure-database-for-mariadb-server"></a>Azure Database for MariaDB-kiszolgáló létrehozása

Hozzon létre egy Azure Database for MariaDB kiszolgálót a `New-AzMariaDbServer` parancsmaggal. Egy kiszolgáló több adatbázist is tud kezelni. Általában külön adatbázissal rendelkezik minden projekt vagy felhasználó.

A következő példa egy MariaDB-kiszolgálót hoz létre az **USA nyugati** régiójában, a **myresourcegroup** erőforráscsoport **mydemoserver** nevű csoportjában, a **myadmin** kiszolgáló-rendszergazdai bejelentkezési azonosítójával. Ez egy általános célú, 2 virtuális mag és a Geo-redundáns biztonsági mentést használó, általános célú árképzési szinten található Gen 5 kiszolgáló. Dokumentálja a példa első sorában használt jelszót, mivel ez a MariaDB-kiszolgáló rendszergazdai fiókjának jelszava.

> [!TIP]
> A kiszolgáló neve egy DNS-névbe van leképezve, ezért globálisan egyedinek kell lennie az Azure-ban.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

Az **SKU** paraméter értéke az egyezmény **díjszabási rétegének \_ számítási-generálási \_ virtuális mag** követi, ahogy az alábbi példákban is látható.

- `-Sku B_Gen5_1` az alapszintű, a Gen 5 és az 1 virtuális mag képezi le. Ez a lehetőség az elérhető legkisebb SKU.
- `-Sku GP_Gen5_32` jelentése: Általános célú, 5. generációs és 32 virtuális mag.
- `-Sku MO_Gen5_2` jelentése: Memóriaoptimalizált, 5. generációs és 2 virtuális mag.

További információ az érvényes **SKU** -értékekről régiónként és rétegek esetén: [Azure Database for MariaDB árképzési szintek](./concepts-pricing-tiers.md).

Érdemes lehet az alapszintű díjszabást használni, ha a számítási feladathoz elegendő a könnyű számítás és az I/O.

> [!IMPORTANT]
> Az alapszintű árképzési szinten létrehozott kiszolgálókat nem lehet az általános célú vagy a memóriára optimalizált, és nem lehet földrajzilag replikálni.

## <a name="configure-a-firewall-rule"></a>Tűzfalszabály konfigurálása

Hozzon létre egy Azure Database for MariaDB kiszolgáló szintű tűzfalszabály a `New-AzMariaDbFirewallRule` parancsmag használatával. A kiszolgálói szintű tűzfalszabályok lehetővé teszik egy külső alkalmazás, például a `mysql` parancssori eszköz vagy a MariaDB Workbench számára a kiszolgálóhoz való kapcsolódást a Azure Database for MariaDB szolgáltatás tűzfalan keresztül.

A következő példa létrehoz egy **AllowMyIP** nevű tűzfalszabály-szabályt, amely lehetővé teszi, hogy egy adott IP-címről (192.168.0.1) érkező kapcsolatokat engedélyezzen. Helyettesítse be az IP-címet vagy IP-címtartományt, amely ahhoz a helyhez tartozik, amelyhez csatlakozik.

```azurepowershell-interactive
New-AzMariaDbFirewallRule -Name AllowMyIP -ResourceGroupName myresourcegroup -ServerName mydemoserver -StartIPAddress 192.168.0.1 -EndIPAddress 192.168.0.1
```

> [!NOTE]
> Az Azure Database for MariaDB kapcsolatai a 3306-os porton keresztül kommunikálnak. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy nem engedélyezett a kimenő forgalom a 3306-as porton keresztül. Ebben az esetben csak akkor csatlakozhat a kiszolgálóhoz, ha az informatikai részleg megnyitja a 3306-es portot.

## <a name="get-the-connection-information"></a>Kapcsolatadatok lekérése

A kiszolgálóhoz való kapcsolódáshoz meg kell adnia a gazdagép adatait és a hozzáférési hitelesítő adatokat. A kapcsolódási adatok megállapításához használja az alábbi példát. Jegyezze fel a **FullyQualifiedDomainName** és a **AdministratorLogin** értékeit.

```azurepowershell-interactive
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Select-Object -Property FullyQualifiedDomainName, AdministratorLogin
```

```Output
FullyQualifiedDomainName                    AdministratorLogin
------------------------                    ------------------
mydemoserver.mariadb.database.azure.com       myadmin
```

## <a name="connect-to-the-server-using-the-mysql-command-line-tool"></a>Kapcsolódás a kiszolgálóhoz a MySQL parancssori eszköz használatával

Kapcsolódjon a kiszolgálóhoz a `mysql` parancssori eszköz használatával. A parancssori eszköz letöltéséhez és telepítéséhez tekintse meg a [MariaDB közösségi letöltések](https://dev.mysql.com/downloads/shell/)című témakört. A Azure Cloud Shell parancssori eszköz előre telepített verzióját is elérheti, ha `mysql` a jelen cikkben található kód **kipróbálása** gombra kattint. A Azure Cloud Shell elérésének egyéb módjai a Azure Portal jobb felső sarkában lévő **>_** gombra kattintva vagy a [shell.Azure.com](https://shell.azure.com/)meglátogatásával érhetők el.

```azurepowershell-interactive
mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="create-a-database"></a>Adatbázis létrehozása

Miután csatlakozott a kiszolgálóhoz, hozzon létre egy üres adatbázist.

```sql
mysql> CREATE DATABASE mysampledb;
```

Futtassa a parancssorban a következő parancsot a kapcsolat az újonnan létrehozott adatbázisra történő átállítására:

```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Táblák létrehozása az adatbázisban

Most, hogy már tudja, hogyan csatlakozhat a Azure Database for MariaDB adatbázishoz, hajtson végre néhány alapvető feladatot.

Először hozzunk létre egy táblát, és töltsük fel adatokkal. Hozzunk létre egy táblát leltáradatok tárolásához.

```sql
CREATE TABLE inventory (
  id serial PRIMARY KEY,
  name VARCHAR(50),
  quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Adatok betöltése a táblákba

Most, hogy már rendelkezünk egy táblával, szúrjunk be néhány adatot. A megnyitott parancssori ablakban futtassa a következő lekérdezést néhány adatsor beszúrásához.

```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150);
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Most két mintaadatsorral rendelkezünk a korábban létrehozott táblában.

## <a name="query-and-update-the-data-in-the-tables"></a>A táblákban lévő adatok lekérdezése és frissítése

Hajtsa végre a következő lekérdezést az adatbázistáblában lévő információk lekéréséhez.

```sql
SELECT * FROM inventory;
```

A táblákban lévő adatokat frissítheti is.

```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

A sor az adatok lekérésekor megfelelően frissül.

```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Adatbázis visszaállítása egy korábbi időpontra

A kiszolgálót visszaállíthatja egy korábbi időpontra. A visszaállított adatfájlokat egy új kiszolgálóra másolja a rendszer, és a meglévő kiszolgáló változatlan marad. Ha például egy tábla véletlenül el van dobva, visszaállíthatja azt az időpontra, ameddig csak a drop történt. Ezután lekérheti a hiányzó táblát és az adatait a kiszolgáló visszaállított példányáról.

A kiszolgáló visszaállításához használja a `Restore-AzMariaDbServer` PowerShell-parancsmagot.

### <a name="run-the-restore-command"></a>A Restore parancs futtatása

A kiszolgáló visszaállításához futtassa a következő példát a PowerShellből.

```azurepowershell-interactive
$restorePointInTime = (Get-Date).AddMinutes(-10)
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMariaDbServer -Name mydemoserver-restored -ResourceGroupName myresourcegroup -RestorePointInTime $restorePointInTime -UsePointInTimeRestore
```

Amikor egy kiszolgálót egy korábbi időpontra állít vissza, létrejön egy új kiszolgáló. Az eredeti kiszolgáló és a megadott időponthoz tartozó adatbázisai az új kiszolgálóra lesznek másolva.

A visszaállított kiszolgáló helye és árképzési szintjei változatlanok maradnak az eredeti kiszolgálóval.

A visszaállítási folyamat befejeződése után keresse meg az új kiszolgálót, és győződjön meg róla, hogy az Adathelyreállítás a várt módon történik. Az új kiszolgáló ugyanazzal a kiszolgáló-rendszergazdai bejelentkezési névvel és jelszóval rendelkezik, amely a visszaállítás elindításának időpontjában érvényes a meglévő kiszolgálóhoz. A jelszót az új kiszolgáló **áttekintő** oldaláról lehet megváltoztatni.

A visszaállítás során létrehozott új kiszolgáló nem rendelkezik az eredeti kiszolgálón található VNet-szolgáltatási végpontokkal. Ezeket a szabályokat külön kell beállítani az új kiszolgálóhoz. A rendszer visszaállítja az eredeti kiszolgáló tűzfalszabályok beállításait.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Azure Database for MariaDB-kiszolgáló biztonsági mentése és visszaállítása a PowerShell használatával](howto-restore-server-powershell.md)
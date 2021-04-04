---
title: 'Gyors útmutató: kiszolgáló létrehozása – Azure PowerShell – Azure Database for MariaDB'
description: Ez a rövid útmutató azt ismerteti, hogyan használható a PowerShell egy Azure Database for MariaDB-kiszolgáló Azure-erőforráscsoportbeli létrehozásához.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurepowershell
ms.topic: quickstart
ms.date: 05/26/2020
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 05082ffa891b72b472ed5433282198c61080f073
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664664"
---
# <a name="quickstart-create-an-azure-database-for-mariadb-server-using-powershell"></a>Rövid útmutató: Azure Database for MariaDB-kiszolgáló létrehozása a PowerShell használatával

Ez a rövid útmutató azt ismerteti, hogyan használható a PowerShell egy Azure Database for MariaDB-kiszolgáló Azure-erőforráscsoportbeli létrehozásához. A PowerShell használatával interaktív módon vagy parancsfájlokban hozhat létre és kezelhet Azure-erőforrásokat.

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

A következő táblázat a parancsmag leggyakrabban használt paramétereinek listáját és a mintavételi értékeket tartalmazza `New-AzMariaDbServer` .

|        **Beállítás**         | **Mintaérték** |                                                                                                                                                             **Leírás**                                                                                                                                                              |
| -------------------------- | ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Name                       | mydemoserver     | Válasszon egy globálisan egyedi nevet az Azure-ban, amely a Azure Database for MariaDB-kiszolgálót azonosítja. A kiszolgáló neve csak betűket, számokat és a kötőjel (-) karaktert tartalmazhatja. A megadott nagybetűs karakterek automatikusan kisbetűsre konvertálódnak a létrehozási folyamat során. 3–63 karakter hosszúságú lehet. |
| ResourceGroupName          | myResourceGroup  | Adja meg az Azure-erőforráscsoport nevét.                                                                                                                                                                                                                                                                                            |
| SKU                        | GP_Gen5_2        | A termékváltozat neve. A következő egyezmény **díjszabása: standard \_ számítási generációs \_ virtuális mag** a gyorsírásban. Az SKU paraméterrel kapcsolatos további információkért tekintse meg a táblázatot követő információkat.                                                                                                                                           |
| BackupRetentionDay         | 7                | Az az időtartam, ameddig egy biztonsági mentést meg kell őrizni. A mértékegysége a nap. A tartomány 7–35.                                                                                                                                                                                                                                                                       |
| GeoRedundantBackup         | Engedélyezve          | Azt adja meg, hogy a georedundáns biztonsági mentést engedélyezni kell-e ehhez a kiszolgálóhoz. Ez az érték nem engedélyezhető a kiszolgálók számára az alapszintű díjszabási szinten, és a kiszolgáló létrehozása után nem módosítható. Megengedett értékek: Engedélyezve, Letiltva.                                                                                                      |
| Hely                   | westus           | A-kiszolgáló Azure-régiója.                                                                                                                                                                                                                                                                                                         |
| SslEnforcement             | Engedélyezve          | Azt jelzi, hogy engedélyezve van-e az SSL, vagy sem ehhez a kiszolgálóhoz. Megengedett értékek: Engedélyezve, Letiltva.                                                                                                                                                                                                                                                 |
| StorageInMb                | 51 200            | A kiszolgáló tárkapacitása (megabájtban megadva). Az érvényes StorageInMb legalább 5120 MB, és 1024 MB-onként növekszik. További információ a tárolási méretekkel kapcsolatos korlátokról: [Azure Database for MariaDB díjszabási szintek](./concepts-pricing-tiers.md).                                                                               |
| Verzió                    | 5.7              | A MariaDB főverziója.                                                                                                                                                                                                                                                                                                                 |
| AdministratorUserName      | myadmin          | A rendszergazda bejelentkezéshez használt felhasználóneve. Nem lehet **azure_superuser**, **admin**, **administrator**, **root**, **guest** vagy **public**.                                                                                                                                                                                            |
| AdministratorLoginPassword | `<securestring>` | A rendszergazda felhasználó jelszava biztonságos sztring formájában. A jelszó 8–128 karakterből állhat. A jelszónak legalább háromféle karaktert tartalmaznia kell a következő kategóriák közül: angol nagybetűs karakterek, angol kisbetűs karakterek, számjegyek és nem alfanumerikus karakterek.                                       |

Az **SKU** paraméter értéke az egyezmény **díjszabási rétegének \_ számítási-generálási \_ virtuális mag** követi, ahogy az alábbi példákban is látható.

- `-Sku B_Gen5_1` az alapszintű, a Gen 5 és az 1 virtuális mag képezi le. Ez a lehetőség az elérhető legkisebb SKU.
- `-Sku GP_Gen5_32` jelentése: Általános célú, 5. generációs és 32 virtuális mag.
- `-Sku MO_Gen5_2` jelentése: Memóriaoptimalizált, 5. generációs és 2 virtuális mag.

További információ az érvényes **SKU** -értékekről régiónként és rétegek esetén: [Azure Database for MariaDB árképzési szintek](./concepts-pricing-tiers.md).

A következő példa egy MariaDB-kiszolgálót hoz létre az **USA nyugati** régiójában, a **myresourcegroup** erőforráscsoport **mydemoserver** nevű csoportjában, a **myadmin** kiszolgáló-rendszergazdai bejelentkezési azonosítójával. Ez egy általános célú, 2 virtuális mag és a Geo-redundáns biztonsági mentést használó, általános célú árképzési szinten található Gen 5 kiszolgáló. Dokumentálja a példa első sorában használt jelszót, mivel ez a MariaDB-kiszolgáló rendszergazdai fiókjának jelszava.

> [!TIP]
> A kiszolgáló neve egy DNS-névbe van leképezve, ezért globálisan egyedinek kell lennie az Azure-ban.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

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

## <a name="configure-ssl-settings"></a>Az SSL-beállítások konfigurálása

Alapértelmezés szerint a kiszolgáló és az ügyfélalkalmazások közti SSL-kapcsolatok kényszerítve vannak. Ez az alapértelmezett beállítás biztosítja a _mozgásban lévő_ adat biztonságát az adatfolyam interneten keresztüli titkosításával. Ehhez a rövid útmutatóhoz tiltsa le az SSL-kapcsolatokat a kiszolgálóján. További információ: [Az SSL-kapcsolatok konfigurálása az alkalmazásban az Azure Database for MariaDB-hez való biztonságos kapcsolódásra](./howto-configure-ssl.md).

> [!WARNING]
> Ennek az SSL-nek a letiltása éles kiszolgálók esetében nem javasolt.

Az alábbi példa letiltja az SSL használatát a Azure Database for MariaDB-kiszolgálón.

```azurepowershell-interactive
Update-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -SslEnforcement Disabled
```

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

Kapcsolódjon a kiszolgálóhoz a `mysql` parancssori eszköz használatával. A parancssori eszköz letöltéséhez és telepítéséhez tekintse meg a [MySQL-Közösség letöltéseit](https://dev.mysql.com/downloads/shell/)ismertető témakört. A Azure Cloud Shell parancssori eszköz előre telepített verzióját is elérheti, ha `mysql` a jelen cikkben található kód **kipróbálása** gombra kattint. A Azure Cloud Shell elérésének egyéb módjai a Azure Portal jobb felső sarkában lévő **>_** gombra kattintva vagy a [shell.Azure.com](https://shell.azure.com/)meglátogatásával érhetők el.

1. Kapcsolódjon a kiszolgálóhoz a `mysql` parancssori eszköz használatával.

   ```azurepowershell-interactive
   mysql -h <servername>.mariadb.database.azure.com -u myadmin@<servername> -p
   ```

1. Kiszolgáló állapotának megtekintése.

   ```sql
   mysql> status
   ```

   ```Output
   C:\Users\>mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
   Enter password: *************
   Welcome to the MySQL monitor.  Commands end with ; or \g.
   Your MySQL connection id is 64793
   Server version: 5.6.42.0 MariaDB Server

   Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

   Oracle is a registered trademark of Oracle Corporation and/or its
   affiliates. Other names may be trademarks of their respective
   owners.

   Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

   mysql> status
   --------------
   /usr/bin/mysql  Ver 14.14 Distrib 5.7.29, for Linux (x86_64) using  EditLine wrapper

   Connection id:          64793
   Current database:
   Current user:           myadmin@myipaddress
   SSL:                    Cipher in use is ECDHE-RSA-AES256-GCM-SHA384
   Current pager:          stdout
   Using outfile:          ''
   Using delimiter:        ;
   Server version:         5.6.42.0 MariaDB Server
   Protocol version:       10
   Connection:             mydemoserver.mariadb.database.azure.com via TCP/IP
   Server characterset:    latin1
   Db     characterset:    latin1
   Client characterset:    utf8
   Conn.  characterset:    utf8
   TCP port:               3306
   Uptime:                 17 min 4 sec

   Threads: 19  Questions: 482  Slow queries: 0  Opens: 50  Flush tables: 3  Open tables: 12  Queries per second avg: 0.470
   --------------

   mysql>
   ```

További parancsokról a [MySQL 5.7 referencia-kézikönyvének 4.5.1 fejezetében](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) tájékozódhat.

## <a name="connect-to-the-server-using-mariadb-workbench"></a>Kapcsolódás a kiszolgálóhoz a MariaDB Workbench használatával

1. Indítsa el a MySQL Workbench alkalmazást az ügyfélszámítógépen. A MySQL Workbench letöltéséhez és telepítéséhez tekintse meg a [MySQL Workbench letöltését](https://dev.mysql.com/downloads/workbench/)ismertető témakört.

1. A **Setup New Connection** (Új kapcsolat beállítása) párbeszédpanelen adja meg a következő információkat a **Parameters** (Paraméterek) lapon:

   ![új kapcsolat beállítása](./media/quickstart-create-mariadb-server-database-using-azure-powershell/setup-new-connection.png)

    |    **Beállítás**    |            **Javasolt érték**            |                      **Leírás**                       |
    | ----------------- | ----------------------------------------- | ---------------------------------------------------------- |
    | Kapcsolat neve   | My Connection                             | Címke megadása a kapcsolatban                        |
    | Connection Method (Kapcsolati módszer) | Standard (TCP/IP)                         | Csatlakozás az Azure Database for MariaDB-hez a TCP/IP protokollal |
    | Hostname (Gazdanév)          | `mydemoserver.mariadb.database.azure.com` | A korábban feljegyzett kiszolgálónév                           |
    | Port              | 3306                                      | A MariaDB alapértelmezett portja                                 |
    | Felhasználónév          | myadmin@mydemoserver                      | Korábban feljegyzett kiszolgáló-rendszergazdai bejelentkezés                |
    | Jelszó          | *************                             | A korábban konfigurált rendszergazdai fiók jelszavának használata      |

1. Ha szeretné ellenőrizni, hogy a paraméterek helyesen vannak-e konfigurálva, kattintson a **Kapcsolódás tesztelése** gombra.

1. Válassza ki a kapcsolatot a kiszolgálóhoz való kapcsolódáshoz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az ebben a rövid útmutatóban létrehozott erőforrások nem szükségesek egy másik gyors útmutatóhoz vagy oktatóanyaghoz, a következő példa futtatásával törölheti őket.

> [!CAUTION]
> A következő példa törli a megadott erőforráscsoportot és a benne található összes erőforrást.
> Ha a rövid útmutató hatókörén kívüli erőforrások szerepelnek a megadott erőforráscsoporthoz, akkor azokat is törli a rendszer.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myresourcegroup
```

Ha csak az ebben a rövid útmutatóban létrehozott kiszolgálót szeretné törölni az erőforráscsoport törlése nélkül, használja a `Remove-AzMariaDbServer` parancsmagot.

```azurepowershell-interactive
Remove-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Azure Database for MariaDB tervezése a PowerShell használatával](tutorial-design-database-using-powershell.md)
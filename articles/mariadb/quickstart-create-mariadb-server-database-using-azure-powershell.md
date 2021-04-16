---
title: 'Rövid útmutató: Kiszolgáló létrehozása – Azure PowerShell – Azure Database for MariaDB'
description: Ez a rövid útmutató azt ismerteti, hogyan hozhat létre Azure Database for MariaDB-kiszolgálót egy Azure-erőforráscsoportban a PowerShell használatával.
author: savjani
ms.author: pariks
ms.date: 05/26/2020
ms.topic: quickstart
ms.service: mariadb
ms.devlang: azurepowershell
ms.custom:
- mvc
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: 7bfbd223cb337ff7d908032c769afbbf5cafa38f
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531383"
---
# <a name="quickstart-create-an-azure-database-for-mariadb-server-using-powershell"></a>Rövid útmutató: Új Azure Database for MariaDB létrehozása a PowerShell használatával

Ez a rövid útmutató azt ismerteti, hogyan hozhat létre Azure Database for MariaDB-kiszolgálót egy Azure-erőforráscsoportban a PowerShell használatával. A PowerShell használatával interaktív módon vagy szkriptekkel hozhat létre és kezelhet Azure-erőforrásokat.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

Ha a PowerShell helyi használatát választja, akkor ehhez a cikkhez telepítenie kell az Az PowerShell modult, és csatlakoznia kell az Azure-fiókjához a [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmaggal. További információ az Az PowerShell-modul telepítéséről: [Install Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Bár az Az.MariaDb PowerShell-modul előzetes verzióban érhető el, az Az PowerShell-modultól külön kell telepítenie a következő paranccsal: `Install-Module -Name Az.MariaDb -AllowPrerelease` .
> Miután az Az.MariaDb PowerShell-modul általánosan elérhetővé válik, a későbbi Az PowerShell-modulok kiadásának része lesz, és natív módon érhető el a Azure Cloud Shell.

Ha első alkalommal használja a Azure Database for MariaDB szolgáltatást, regisztrálnia kell a **Microsoft.DBforMariaDB** erőforrás-szolgáltatót.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.DBforMariaDB
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Ha több Azure-előfizetéssel rendelkezik, válassza ki a megfelelő előfizetést, amelyben az erőforrásokat ki kell számlázni. Válasszon ki egy adott előfizetés-azonosítót a [Set-AzContext](/powershell/module/az.accounts/set-azcontext) parancsmag használatával.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre [egy Azure-erőforráscsoportot](../azure-resource-manager/management/overview.md) a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancsmag használatával. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer csoportként helyezi üzembe és kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy **myresourcegroup nevű erőforráscsoportot** az **USA nyugati régiójában.**

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroup -Location westus
```

## <a name="create-an-azure-database-for-mariadb-server"></a>Azure Database for MariaDB-kiszolgáló létrehozása

Hozzon létre Azure Database for MariaDB-kiszolgálót a `New-AzMariaDbServer` parancsmaggal. Egy kiszolgáló több adatbázist is tud kezelni. Általában külön adatbázissal rendelkezik minden projekt vagy felhasználó.

Az alábbi táblázat a parancsmag gyakran használt paramétereinek és mintaértékének `New-AzMariaDbServer` listáját tartalmazza.

|        **Beállítás**         | **Mintaérték** |                                                                                                                                                             **Leírás**                                                                                                                                                              |
| -------------------------- | ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Name                       | mydemoserver     | Válasszon egy globálisan egyedi nevet az Azure-ban, amely azonosítja a Azure Database for MariaDB kiszolgálót. A kiszolgáló neve csak betűket, számokat és a kötőjel (-) karaktert tartalmazhatja. A rendszer automatikusan kisbetűssé alakítja a megadott nagybetűket a létrehozási folyamat során. 3–63 karakter hosszúságú lehet. |
| ResourceGroupName          | myResourceGroup  | Adja meg az Azure-erőforráscsoport nevét.                                                                                                                                                                                                                                                                                            |
| SKU                        | GP_Gen5_2        | A termékváltozat neve. Röviden követi **a tarifacsomagú \_ számítási generációs \_ virtuális** magok konvencióját. A termékváltozat paraméterrel kapcsolatos további információkért tekintse meg az alábbi táblázatban található információkat.                                                                                                                                           |
| BackupRetentionDay         | 7                | Az az időtartam, ameddig egy biztonsági mentést meg kell őrizni. A mértékegysége a nap. A tartomány 7–35.                                                                                                                                                                                                                                                                       |
| GeoRedundantBackup         | Engedélyezve          | Azt adja meg, hogy a georedundáns biztonsági mentést engedélyezni kell-e ehhez a kiszolgálóhoz. Ez az érték nem engedélyezhető az alapszintű tarifacsomagban található kiszolgálókon, és a kiszolgáló létrehozása után már nem módosítható. Megengedett értékek: Engedélyezve, Letiltva.                                                                                                      |
| Hely                   | westus           | A kiszolgáló Azure-régiója.                                                                                                                                                                                                                                                                                                         |
| SslEnforcement             | Engedélyezve          | Azt határozza meg, hogy engedélyezni kell-e az SSL-t ehhez a kiszolgálóhoz. Megengedett értékek: Engedélyezve, Letiltva.                                                                                                                                                                                                                                                 |
| StorageInMb                | 51 200            | A kiszolgáló tárkapacitása (megabájtban megadva). Az érvényes StorageInMb legalább 5120 MB méretű, és 1024 MB-os növekményekkel növekszik. A tárterület méretkorlátokkal kapcsolatos további információkért [lásd: Azure Database for MariaDB tarifacsomagok.](./concepts-pricing-tiers.md)                                                                               |
| Verzió                    | 5.7              | A MariaDB főverziója.                                                                                                                                                                                                                                                                                                                 |
| AdministratorUserName (Rendszergazda felhasználóneve)      | myadmin          | A rendszergazda bejelentkezéshez használt felhasználóneve. Nem lehet **azure_superuser**, **admin**, **administrator**, **root**, **guest** vagy **public**.                                                                                                                                                                                            |
| AdministratorLoginPassword | `<securestring>` | A rendszergazdai felhasználó jelszava egy biztonságos sztring formájában. A jelszó 8–128 karakterből állhat. A jelszónak legalább háromféle karaktert tartalmaznia kell a következő kategóriák közül: angol nagybetűs karakterek, angol kisbetűs karakterek, számjegyek és nem alfanumerikus karakterek.                                       |

A **termékváltozat paraméterértéke** a tarifacsomagú számítási generációs **\_ \_ virtuális** magok konvencióját követi, ahogy az alábbi példákban látható.

- `-Sku B_Gen5_1` A alapszintű, 5. generációs és 1 virtuális magot tartalmaz. Ez a lehetőség a legkisebb elérhető termékváltozat.
- `-Sku GP_Gen5_32` jelentése: Általános célú, 5. generációs és 32 virtuális mag.
- `-Sku MO_Gen5_2` jelentése: Memóriaoptimalizált, 5. generációs és 2 virtuális mag.

További információ az érvényes **termékváltozat-értékekről** régiónként és szint szerint: Azure Database for MariaDB [tarifacsomagok.](./concepts-pricing-tiers.md)

Az alábbi példa egy **mydemoserver** nevű MariaDB-kiszolgálót hoz létre az USA nyugati régiójában a **myresourcegroup** erőforráscsoportban a myadmin kiszolgálói rendszergazdai bejelentkezési **adataival.**  Ez egy 5. generációs kiszolgáló az általános célú tarifacsomagban, 2 virtuális maggal és georedundáns biztonsági mentéssel. Dokumentálja a példa első sorában használt jelszót, mivel ez a MariaDB-kiszolgálói rendszergazdai fiók jelszava.

> [!TIP]
> A kiszolgáló neve egy DNS-névbe van leképezve, ezért globálisan egyedinek kell lennie az Azure-ban.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

Érdemes lehet az alapszintű tarifacsomagot használni, ha a kis számítási és I/O-kapacitás megfelelő a számítási feladathoz.

> [!IMPORTANT]
> Az alapszintű tarifacsomagban létrehozott kiszolgálók később nem skálázhatók általános célú vagy memóriaoptimalikusra, és nem replikálhatók georeplikáltra.

## <a name="configure-a-firewall-rule"></a>Tűzfalszabály konfigurálása

Hozzon létre Azure Database for MariaDB kiszolgálószintű tűzfalszabályt a `New-AzMariaDbFirewallRule` parancsmag használatával. A kiszolgálószintű tűzfalszabályok lehetővé teszik, hogy külső alkalmazások, például a parancssori eszköz vagy a MariaDB Workbench csatlakozzon a kiszolgálóhoz az `mysql` Azure Database for MariaDB szolgáltatás tűzfalán keresztül.

Az alábbi példa egy **AllowMyIP** nevű tűzfalszabályt hoz létre, amely engedélyezi a 192.168.0.1 IP-címről való csatlakozást. Helyettesítse be azt az IP-címet vagy IP-címtartományt, amely megfelel annak a helynek, amelyről csatlakozik.

```azurepowershell-interactive
New-AzMariaDbFirewallRule -Name AllowMyIP -ResourceGroupName myresourcegroup -ServerName mydemoserver -StartIPAddress 192.168.0.1 -EndIPAddress 192.168.0.1
```

> [!NOTE]
> Az Azure Database for MariaDB kapcsolatai a 3306-os porton keresztül kommunikálnak. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy nem engedélyezett a kimenő forgalom a 3306-as porton keresztül. Ebben a forgatókönyvben csak akkor tud csatlakozni a kiszolgálóhoz, ha az it-részleg megnyitja a 3306-os portot.

## <a name="configure-ssl-settings"></a>Az SSL-beállítások konfigurálása

Alapértelmezés szerint a kiszolgáló és az ügyfélalkalmazások közti SSL-kapcsolatok kényszerítve vannak. Ez az alapértelmezett  beállítás biztosítja a mozgásban található adatok biztonságát az adatfolyam interneten keresztüli titkosításával. Ehhez a rövid útmutatóhoz tiltsa le az SSL-kapcsolatokat a kiszolgálóján. További információ: [Az SSL-kapcsolatok konfigurálása az alkalmazásban az Azure Database for MariaDB-hez való biztonságos kapcsolódásra](./howto-configure-ssl.md).

> [!WARNING]
> Ennek az SSL-nek a letiltása éles kiszolgálók esetében nem javasolt.

A következő példa letiltja az SSL-t a Azure Database for MariaDB kiszolgálón.

```azurepowershell-interactive
Update-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -SslEnforcement Disabled
```

## <a name="get-the-connection-information"></a>Kapcsolatadatok lekérése

A kiszolgálóhoz való kapcsolódáshoz meg kell adnia a gazdagép adatait és a hozzáférési hitelesítő adatokat. A kapcsolati adatok meghatározásához használja az alábbi példát. Jegyezze fel a **FullyQualifiedDomainName** és **az AdministratorLogin értékeit.**

```azurepowershell-interactive
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Select-Object -Property FullyQualifiedDomainName, AdministratorLogin
```

```Output
FullyQualifiedDomainName                    AdministratorLogin
------------------------                    ------------------
mydemoserver.mariadb.database.azure.com       myadmin
```

## <a name="connect-to-the-server-using-the-mysql-command-line-tool"></a>Csatlakozás a kiszolgálóhoz a mysql parancssori eszköz használatával

Csatlakozzon a kiszolgálóhoz a `mysql` parancssori eszköz használatával. A parancssori eszköz letöltéséhez és telepítéséhez lásd: [MySQL Community Downloads (MySQL-közösség letöltése).](https://dev.mysql.com/downloads/shell/) A parancssori eszköz egy előre telepített verzióját is elérheti a Azure Cloud Shell a cikkben található kódminta Kipróbálom `mysql` gombját választva.  A hozzáférés további Azure Cloud Shell a jobb  felső eszköztár>_ gombjával, vagy a Azure Portal a [shell.azure.com.](https://shell.azure.com/)

1. Csatlakozzon a kiszolgálóhoz `mysql` a parancssori eszközzel.

   ```azurepowershell-interactive
   mysql -h <servername>.mariadb.database.azure.com -u myadmin@<servername> -p
   ```

1. A kiszolgáló állapotának megtekintése.

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

## <a name="connect-to-the-server-using-mariadb-workbench"></a>Csatlakozás a kiszolgálóhoz a MariaDB Workbench használatával

1. Indítsa el a MySQL Workbench alkalmazást az ügyfélszámítógépen. A MySQL Workbench letöltéséhez és telepítéséhez lásd: [A MySQL Workbench letöltése.](https://dev.mysql.com/downloads/workbench/)

1. A **Setup New Connection** (Új kapcsolat beállítása) párbeszédpanelen adja meg a következő információkat a **Parameters** (Paraméterek) lapon:

   ![új kapcsolat beállítása](./media/quickstart-create-mariadb-server-database-using-azure-powershell/setup-new-connection.png)

    |    **Beállítás**    |            **Javasolt érték**            |                      **Leírás**                       |
    | ----------------- | ----------------------------------------- | ---------------------------------------------------------- |
    | Kapcsolat neve   | My Connection                             | Címke megadása ehhez a kapcsolathoz                        |
    | Connection Method (Kapcsolati módszer) | Standard (TCP/IP)                         | Csatlakozás az Azure Database for MariaDB-hez a TCP/IP protokollal |
    | Hostname (Gazdanév)          | `mydemoserver.mariadb.database.azure.com` | Korábban feljegyzett kiszolgálónév                           |
    | Port              | 3306                                      | A MariaDB alapértelmezett portja                                 |
    | Felhasználónév          | myadmin@mydemoserver                      | A korábban feljegyzett kiszolgáló-rendszergazdai bejelentkezés                |
    | Jelszó          | *************                             | A korábban konfigurált rendszergazdai fiók jelszavának használata      |

1. Annak tesztelésére, hogy a paraméterek megfelelően vannak-e konfigurálva, kattintson a **Kapcsolat tesztelése gombra.**

1. Válassza ki a kiszolgálóhoz való csatlakozáshoz szükséges kapcsolatot.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az ebben a rövid útmutatóban létrehozott erőforrásokra nincs szükség egy másik rövid útmutatóhoz vagy oktatóanyaghoz, az alábbi példa futtatásával törölheti őket.

> [!CAUTION]
> Az alábbi példa törli a megadott erőforráscsoportot és az összes benne lévő erőforrást.
> Ha a rövid útmutató hatókörében kívüli erőforrások léteznek a megadott erőforráscsoportban, akkor azok is törlődnek.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myresourcegroup
```

Ha csak az ebben a rövid útmutatóban létrehozott kiszolgálót szeretné törölni az erőforráscsoport törlése nélkül, használja a `Remove-AzMariaDbServer` parancsmagot.

```azurepowershell-interactive
Remove-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Alkalmazás Azure Database for MariaDB PowerShell használatával](tutorial-design-database-using-powershell.md)

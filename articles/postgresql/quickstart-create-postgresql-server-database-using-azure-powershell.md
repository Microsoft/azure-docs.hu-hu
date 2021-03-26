---
title: 'Gyors útmutató: kiszolgáló létrehozása – Azure PowerShell-Azure Database for PostgreSQL – egyetlen kiszolgáló'
description: Rövid útmutató egy Azure Database for PostgreSQL – egyetlen kiszolgáló létrehozásához Azure PowerShell használatával.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurepowershell
ms.topic: quickstart
ms.date: 06/08/2020
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 79a070dbd49ed6ee00dd790c8157057aebde30df
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105606735"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---single-server-using-powershell"></a>Rövid útmutató: Azure Database for PostgreSQL egyetlen kiszolgáló létrehozása a PowerShell használatával

Ez a rövid útmutató azt ismerteti, hogyan használható a PowerShell egy Azure Database for PostgreSQL-kiszolgáló Azure-erőforráscsoportbeli létrehozásához. A PowerShell használatával interaktív módon vagy parancsfájlokban hozhat létre és kezelhet Azure-erőforrásokat.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

Ha a PowerShell helyi használatát választja, akkor ehhez a cikkhez telepítenie kell az az PowerShell-modult, és csatlakoznia kell az Azure-fiókjához a [AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmag használatával. Az az PowerShell-modul telepítésével kapcsolatos további információkért lásd: [Install Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Az az. PostgreSql PowerShell-modul előzetes verzióban érhető el, és a következő paranccsal külön kell telepítenie az az PowerShell-modulból: `Install-Module -Name Az.PostgreSql -AllowPrerelease` .
> Amint az az. PostgreSql PowerShell-modul általánosan elérhetővé válik, az a PowerShell modul kiadásainak része lesz, és natív módon elérhető a Azure Cloud Shellon belülről.

Ha első alkalommal használja a Azure Database for PostgreSQL szolgáltatást, regisztrálnia kell a **Microsoft. DBforPostgreSQL** erőforrás-szolgáltatót.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.DBforPostgreSQL
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

## <a name="create-an-azure-database-for-postgresql-server"></a>Azure-adatbázis létrehozása PostgreSQL-kiszolgálóhoz

Hozzon létre egy Azure Database for PostgreSQL kiszolgálót a `New-AzPostgreSqlServer` parancsmaggal. Egy kiszolgáló több adatbázist is tud kezelni. Általában külön adatbázissal rendelkezik minden projekt vagy felhasználó.

A következő táblázat a parancsmag leggyakrabban használt paramétereinek listáját és a mintavételi értékeket tartalmazza `New-AzPostgreSqlServer` .

|        **Beállítás**         | **Mintaérték** |                                                                                                                                                             **Leírás**                                                                                                                                                              |
| -------------------------- | ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Name                       | mydemoserver     | Válasszon egy globálisan egyedi nevet az Azure-ban, amely a Azure Database for PostgreSQL-kiszolgálót azonosítja. A kiszolgáló neve csak betűket, számokat és a kötőjel (-) karaktert tartalmazhatja. A megadott nagybetűs karakterek automatikusan kisbetűsre konvertálódnak a létrehozási folyamat során. 3–63 karakter hosszúságú lehet. |
| ResourceGroupName          | myResourceGroup  | Adja meg az Azure-erőforráscsoport nevét.                                                                                                                                                                                                                                                                                            |
| SKU                        | GP_Gen5_2        | A termékváltozat neve. A következő egyezmény **díjszabása: standard \_ számítási generációs \_ virtuális mag** a gyorsírásban. Az SKU paraméterrel kapcsolatos további információkért tekintse meg a táblázatot követő információkat.                                                                                                                                           |
| BackupRetentionDay         | 7                | Az az időtartam, ameddig egy biztonsági mentést meg kell őrizni. A mértékegysége a nap. A tartomány 7–35.                                                                                                                                                                                                                                                                       |
| GeoRedundantBackup         | Engedélyezve          | Azt adja meg, hogy a georedundáns biztonsági mentést engedélyezni kell-e ehhez a kiszolgálóhoz. Ez az érték nem engedélyezhető a kiszolgálók számára az alapszintű díjszabási szinten, és a kiszolgáló létrehozása után nem módosítható. Megengedett értékek: Engedélyezve, Letiltva.                                                                                                      |
| Hely                   | westus           | A-kiszolgáló Azure-régiója.                                                                                                                                                                                                                                                                                                         |
| SslEnforcement             | Engedélyezve          | Azt jelzi, hogy engedélyezve van-e az SSL, vagy sem ehhez a kiszolgálóhoz. Megengedett értékek: Engedélyezve, Letiltva.                                                                                                                                                                                                                                                 |
| StorageInMb                | 51 200            | A kiszolgáló tárkapacitása (megabájtban megadva). Az érvényes StorageInMb legalább 5120 MB, és 1024 MB-onként növekszik. További információ a tárolási méretekkel kapcsolatos korlátokról: [Azure Database for PostgreSQL díjszabási szintek](./concepts-pricing-tiers.md).                                                                               |
| Verzió                    | 9.6              | A PostgreSQL főverziója.                                                                                                                                                                                                                                                                                                                 |
| AdministratorUserName      | myadmin          | A rendszergazda bejelentkezéshez használt felhasználóneve. Nem lehet **azure_superuser**, **admin**, **administrator**, **root**, **guest** vagy **public**.                                                                                                                                                                                            |
| AdministratorLoginPassword | `<securestring>` | A rendszergazda felhasználó jelszava biztonságos sztring formájában. A jelszó 8–128 karakterből állhat. A jelszónak legalább háromféle karaktert tartalmaznia kell a következő kategóriák közül: angol nagybetűs karakterek, angol kisbetűs karakterek, számjegyek és nem alfanumerikus karakterek.                                       |

Az **SKU** paraméter értéke az egyezmény **díjszabási rétegének \_ számítási-generálási \_ virtuális mag** követi, ahogy az alábbi példákban is látható.

- `-Sku B_Gen5_1` az alapszintű, a Gen 5 és az 1 virtuális mag képezi le. Ez a lehetőség az elérhető legkisebb SKU.
- `-Sku GP_Gen5_32` jelentése: Általános célú, 5. generációs és 32 virtuális mag.
- `-Sku MO_Gen5_2` jelentése: Memóriaoptimalizált, 5. generációs és 2 virtuális mag.

További információ az érvényes **SKU** -értékekről régiónként és rétegek esetén: [Azure Database for PostgreSQL árképzési szintek](./concepts-pricing-tiers.md).

A következő példa létrehoz egy PostgreSQL-kiszolgálót az **USA nyugati** régiójában, a **mydemoserver** nevű **myresourcegroup** -erőforráscsoport pedig a **myadmin** kiszolgáló-rendszergazdai bejelentkezési felhasználónevét. Ez egy általános célú, 2 virtuális mag és a Geo-redundáns biztonsági mentést használó, általános célú árképzési szinten található Gen 5 kiszolgáló. Dokumentálja a példa első sorában használt jelszót, mivel ez a PostgreSQL-kiszolgáló rendszergazdai fiókjának jelszava.

> [!TIP]
> A kiszolgáló neve egy DNS-névbe van leképezve, ezért globálisan egyedinek kell lennie az Azure-ban.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

Érdemes lehet az alapszintű díjszabást használni, ha a számítási feladathoz elegendő a könnyű számítás és az I/O.

> [!IMPORTANT]
> Az alapszintű árképzési szinten létrehozott kiszolgálókat nem lehet az általános célú vagy a memóriára optimalizált, és nem lehet földrajzilag replikálni.

## <a name="configure-a-firewall-rule"></a>Tűzfalszabály konfigurálása

Hozzon létre egy Azure Database for PostgreSQL kiszolgáló szintű tűzfalszabály a `New-AzPostgreSqlFirewallRule` parancsmag használatával. A kiszolgálói szintű tűzfalszabályok lehetővé teszik a külső alkalmazások, például a `psql` parancssori eszköz vagy a PostgreSQL Workbench számára a kiszolgálóhoz való kapcsolódást a Azure Database for PostgreSQL szolgáltatás tűzfalan keresztül.

A következő példa létrehoz egy **AllowMyIP** nevű tűzfalszabály-szabályt, amely lehetővé teszi, hogy egy adott IP-címről (192.168.0.1) érkező kapcsolatokat engedélyezzen. Helyettesítse be az IP-címet vagy IP-címtartományt, amely ahhoz a helyhez tartozik, amelyhez csatlakozik.

```azurepowershell-interactive
New-AzPostgreSqlFirewallRule -Name AllowMyIP -ResourceGroupName myresourcegroup -ServerName mydemoserver -StartIPAddress 192.168.0.1 -EndIPAddress 192.168.0.1
```

> [!NOTE]
> Kapcsolatok Azure Database for PostgreSQL a 5432-es porton keresztüli kommunikációhoz. Ha vállalati hálózaton belülről próbál csatlakozni, lehet, hogy a 5432-as porton keresztüli kimenő forgalom nem engedélyezett. Ebben az esetben csak akkor csatlakozhat a kiszolgálóhoz, ha az informatikai részleg megnyitja a 5432-es portot.

## <a name="get-the-connection-information"></a>Kapcsolatadatok lekérése

A kiszolgálóhoz való kapcsolódáshoz meg kell adnia a gazdagép adatait és a hozzáférési hitelesítő adatokat. A kapcsolódási adatok megállapításához használja az alábbi példát. Jegyezze fel a **FullyQualifiedDomainName** és a **AdministratorLogin** értékeit.

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Select-Object -Property FullyQualifiedDomainName, AdministratorLogin
```

```Output
FullyQualifiedDomainName                    AdministratorLogin
------------------------                    ------------------
mydemoserver.postgres.database.azure.com       myadmin
```

## <a name="connect-to-postgresql-database-using-psql"></a>Csatlakozás a PostgreSQL-adatbázishoz a psql használatával

Ha az ügyfélszámítógépen telepítve van a PostgreSQL, akkor használhatja a [psql](https://www.postgresql.org/docs/current/static/app-psql.html) helyi példányát az Azure PostgreSQL-kiszolgálóhoz való csatalakozáshoz. A Azure Cloud Shell parancssori eszköz előre telepített verzióját is elérheti, ha `psql` a jelen cikkben található kód **kipróbálása** gombra kattint. A Azure Cloud Shell elérésének egyéb módjai a Azure Portal jobb felső sarkában lévő **>_** gombra kattintva vagy a [shell.Azure.com](https://shell.azure.com/)meglátogatásával érhetők el.

1. Kapcsolódjon az Azure PostgreSQL-kiszolgálóhoz a `psql` parancssori segédprogram használatával.

   ```azurepowershell-interactive
   psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
   ```

   A következő parancs például a **postgres** nevű alapértelmezett adatbázishoz kapcsolódik a PostgreSQL-kiszolgálón a `mydemoserver.postgres.database.azure.com` hozzáférési hitelesítő adatok használatával. Adja meg a `<server_admin_password>` kiszolgálói rendszergazdai jelszót, amelyet a jelszó megadásakor választott.

   ```azurepowershell-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   > [!TIP]
   > Ha inkább URL-elérési utat szeretne használni a postgres-hez való kapcsolódáshoz, az URL-cím kódolja a @ Sign nevet a következővel: `%40` . Például a psql tartozó kapcsolatok karakterlánca a következő:. `psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres`

1. Miután csatlakozott a kiszolgálóhoz, hozzon létre egy üres adatbázist, amikor a rendszer kéri.

   ```sql
   CREATE DATABASE mypgsqldb;
   ```

1. Amikor a rendszer kéri, hajtsa végre a következő parancsot, hogy az újonnan létrehozott **mypgsqldb** adatbázishoz kapcsolódhasson:

   ```sql
   \c mypgsqldb
   ```

## <a name="connect-to-the-postgresql-server-using-pgadmin"></a>Csatlakozás a PostgreSQL-kiszolgálóhoz a pgAdmin segítségével

A pgAdmin egy nyílt forráskódú eszköz, amely a PostgreSQL-lel együtt használható. A pgAdmin alkalmazást a [pgAdmin webhelyről](https://www.pgadmin.org/) telepítheti. Elképzelhető, hogy az Ön által használt pgAdmin-verzió eltér a jelen gyors útmutatóban használttól. Ha további információra van szüksége, tekintse meg a pgAdmin dokumentációját.

1. Nyissa meg a pgAdmin alkalmazást az ügyfélszámítógépen.

1. Az eszköztáron kattintson az **Objektum** elemre, vigye az egérmutatót a **Létrehozás** fölé, majd válassza a **Kiszolgáló** lehetőséget.

1. A **Létrehozás – Kiszolgáló** párbeszédpanel **Általános** lapján adjon meg egy egyedi rövid nevet a kiszolgáló számára, például **mydemoserver**.

   :::image type="content" source="./media/quickstart-create-postgresql-server-database-using-azure-powershell/9-pgadmin-create-server.png" alt-text="Az általános lap":::

1. A **Létrehozás – Kiszolgáló** párbeszédpanel **Kapcsolat** lapján töltse ki a beállítások tábláját.

   :::image type="content" source="./media/quickstart-create-postgresql-server-database-using-azure-powershell/10-pgadmin-create-server.png" alt-text="A kapcsolatok lap":::

    pgAdmin-paraméter |Érték|Leírás
    ---|---|---
    Gazdagépnév/-cím | Kiszolgálónév | Az a kiszolgálónév, amelyet korábban az Azure Database for PostgreSQL-kiszolgáló létrehozásakor használt. A példakiszolgáló a **mydemoserver.postgres.database.azure.com.** Használja a teljes tartománynevet (**\* . postgres.database.Azure.com**) a példában látható módon. Ha nem emlékszik a kiszolgáló nevére, a kapcsolati adatok lekéréséhez kövesse az előző szakasz lépéseit.
    Port | 5432 | Az Azure Database for PostgreSQL-kiszolgálóhoz való csatlakozáskor használt port.
    Karbantartási adatbázis | *postgres* | A rendszer által létrehozott alapértelmezett adatbázisnév.
    Felhasználónév | Kiszolgáló-rendszergazdai bejelentkezési név | A kiszolgáló-rendszergazdai bejelentkezési felhasználónév, amelyet korábban az Azure Database for PostgreSQL-kiszolgáló létrehozásakor adott meg. Ha nem emlékszik a felhasználónévre, a kapcsolati adatok lekéréséhez kövesse az előző szakasz lépéseit. A formátum a *username \@ servername*.
    Jelszó | Az Ön rendszergazdai jelszava | A rövid útmutatóban a korábbiakban a kiszolgáló létrehozásakor választott jelszó.
    Szerepkör | Hagyja üresen | Itt nem kell megadni szerepkörnevet. Hagyja üresen ezt a mezőt.
    SSL-mód | *Kötelező* | A TLS/SSL üzemmódot a pgAdmin SSL lapján állíthatja be. Alapértelmezés szerint az összes Azure Database for PostgreSQL-kiszolgáló a TLS-kényszerítéssel lett létrehozva. A TLS-kényszerítés kikapcsolásához lásd: [a TLS kényszerítésének konfigurálása](./concepts-ssl-connection-security.md#configure-enforcement-of-tls).

1. Kattintson a **Mentés** gombra.

1. A bal oldali **Böngésző** panelen bontsa ki a **Kiszolgálók** csomópontot. Válassza ki például a **mydemoserver** kiszolgálót. Kattintson rá a csatlakozáshoz.

1. Bontsa ki a kiszolgáló-csomópontot, majd bontsa ki az abban található **Adatbázisok** csomópontot is. A listában szerepelnie kell a meglévő *postgres* adatbázisnak, valamint minden más létrehozott adatbázisnak. Az Azure Database for PostgreSQL segítségével kiszolgálónként több adatbázist is létrehozhat.

1. Kattintson a jobb gombbal az **Adatbázisok** elemre, majd válassza a **Létrehozás** menü **Adatbázis** elemét.

1. Írjon be egy tetszőleges adatbázisnevet az **Adatbázis** mezőbe (például **mypgsqldb2**).

1. A listából válassza ki az adatbázis **tulajdonosát**. Válassza ki a kiszolgáló-rendszergazdai bejelentkezési nevet (a példánkban ez a **my admin**).

   :::image type="content" source="./media/quickstart-create-postgresql-server-database-using-azure-powershell/11-pgadmin-database.png" alt-text="Adatbázis létrehozása a pgAdmin-ben":::

1. Egy új, üres adatbázis létrehozásához válassza a **Mentés** lehetőséget.

1. A **Böngésző** panelen megtekintheti az Ön által létrehozott adatbázist az adatbázisok listájában a kiszolgáló nevét viselő területen.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az ebben a rövid útmutatóban létrehozott erőforrások nem szükségesek egy másik gyors útmutatóhoz vagy oktatóanyaghoz, a következő példa futtatásával törölheti őket.

> [!CAUTION]
> A következő példa törli a megadott erőforráscsoportot és a benne található összes erőforrást.
> Ha a rövid útmutató hatókörén kívüli erőforrások szerepelnek a megadott erőforráscsoporthoz, akkor azokat is törli a rendszer.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myresourcegroup
```

Ha csak az ebben a rövid útmutatóban létrehozott kiszolgálót szeretné törölni az erőforráscsoport törlése nélkül, használja a `Remove-AzPostgreSqlServer` parancsmagot.

```azurepowershell-interactive
Remove-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Azure Database for PostgreSQL tervezése a PowerShell használatával](tutorial-design-database-using-powershell.md)
---
title: 'Oktatóanyag: PHP-alkalmazás és MySQL'
description: Megismerheti, hogyan tehet szert egy olyan, az Azure-ban működő PHP-alkalmazásra, amely csatlakozik egy MySQL-adatbázishoz az Azure-ban. Az oktatóanyag a Laravelt használja.
ms.assetid: 14feb4f3-5095-496e-9a40-690e1414bd73
ms.devlang: php
ms.topic: tutorial
ms.date: 06/15/2020
ms.custom: mvc, cli-validate, seodec18, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 0810f023f4e2e192f2cb0d83f2a028cdded9e275
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779480"
---
# <a name="tutorial-build-a-php-and-mysql-app-in-azure-app-service"></a>Oktatóanyag: PHP- és MySQL-alkalmazás Azure App Service

::: zone pivot="platform-windows"  

[Azure App Service](overview.md) egy nagymértékben skálázható, önjavítást használó webes üzemeltetési szolgáltatást nyújt a Windows operációs rendszer használatával. Ez az oktatóanyag bemutatja, hogyan hozhat létre EGY PHP-alkalmazást az Azure-ban, és hogyan csatlakoztathatja azt egy MySQL-adatbázishoz. Ha végzett, egy Windows rendszeren futó [Laravel-Azure App Service](https://laravel.com/) fog futni.

::: zone-end

::: zone pivot="platform-linux"

[Azure App Service](overview.md) egy nagymértékben skálázható, önjavítást használó webes üzemeltetési szolgáltatást nyújt a Linux operációs rendszer használatával. Ez az oktatóanyag bemutatja, hogyan hozhat létre EGY PHP-alkalmazást az Azure-ban, és hogyan csatlakoztathatja azt egy MySQL-adatbázishoz. Ha elkészült, egy [Laravel-alkalmazás](https://laravel.com/) fog futni a Azure App Service on Linux.

::: zone-end

:::image type="content" source="./media/tutorial-php-mysql-app/complete-checkbox-published.png" alt-text="Képernyőkép egy PHP-alkalmazásról, Feladatlista.":::

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * MySQL-adatbázis létrehozása az Azure-ban
> * PHP-alkalmazás csatlakoztatása a MySQL-hez
> * Az alkalmazás üzembe helyezése az Azure-ban
> * Az adatmodell frissítése és az alkalmazás ismételt üzembe helyezése
> * Diagnosztikai naplók streamelése az Azure-ból
> * Az alkalmazás kezelése az Azure Portalon

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

- [A Git telepítése](https://git-scm.com/)
- [Telepítse a PHP 5.6.4-es vagy újabb verzióját](https://php.net/downloads.php)
- [Telepítse a Composert](https://getcomposer.org/doc/00-intro.md)
- Engedélyezze a következő PHP-bővítményeket, amelyre a Laravelnek szüksége van: OpenSSL, PDO-MySQL, Mbstring, Tokenizer, XML
- [A MySQL telepítése és indítsa el](https://dev.mysql.com/doc/refman/5.7/en/installing.html)
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)] 

## <a name="prepare-local-mysql"></a>A helyi MySQL előkészítése

Ebben a lépésben egy adatbázist hoz létre a helyi MySQL-kiszolgálón, hogy felhasználhassa azt az oktatóanyagban.

### <a name="connect-to-local-mysql-server"></a>Csatlakozás a helyi MySQL-kiszolgálóhoz

Csatlakozzon a helyi MySQL-kiszolgálóhoz egy terminálablakban. Ezzel a terminálablakkal futtathatja az oktatóanyagban szereplő összes parancsot.

```bash
mysql -u root -p
```

Ha a rendszer jelszót kér, adja meg a `root` fiókhoz tartozó jelszót. Ha nem emlékszik a rendszergazdafiók jelszavára, tekintse meg a [MySQL-ben a rendszergazdai jelszó alaphelyzetbe állítását](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html) ismertető cikket.

Ha a parancs futtatása sikeres, akkor a MySQL-kiszolgáló fut. Ha nem, mindenképp a [MySQL telepítése utáni lépéseket](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html) követve indítsa el a helyi MySQL-kiszolgálót.

### <a name="create-a-database-locally"></a>Adatbázis létrehozása helyben

A `mysql` parancssorban hozzon létre egy adatbázist.

```sql 
CREATE DATABASE sampledb;
```

A kiszolgálókapcsolat bontásához írja be a `quit` parancsot.

```sql
quit
```

<a name="step2"></a>

## <a name="create-a-php-app-locally"></a>PHP-alkalmazás létrehozása helyben
Ebben a lépésben egy Laravel-mintaalkalmazásra tesz szert, konfigurálja annak adatbázis-kapcsolatát, és helyileg futtatja az alkalmazást. 

### <a name="clone-the-sample"></a>A minta klónozása

A terminálablakban a `cd` paranccsal lépjen egy munkakönyvtárra.

Futtassa a következő parancsot a mintatárház klónozásához.

```bash
git clone https://github.com/Azure-Samples/laravel-tasks
```

A `cd` paranccsal lépjen a klónozott könyvtárra.
Telepítse a szükséges csomagokat.

```bash
cd laravel-tasks
composer install
```

### <a name="configure-mysql-connection"></a>MySQL-kapcsolat konfigurálása

Hozzon létre egy *.env* nevű fájlt az adattár gyökérkönyvtárjában. Másolja az alábbi változókat a *.env* fájlba. Cserélje le _&lt; root_password>_ helyőrzőt a MySQL-gyökérfelhasználó jelszavára.

```txt
APP_ENV=local
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_DATABASE=sampledb
DB_USERNAME=root
DB_PASSWORD=<root_password>
```

A [Laravel-környezet konfigurációjával](https://laravel.com/docs/5.4/configuration#environment-configuration) foglalkozó cikkben tekinthet meg információt arról, hogy a Laravel hogyan használja a _.env_ fájlt.

### <a name="run-the-sample-locally"></a>A minta futtatása helyben

Futtassa a [Laravel adatbázis-migrálási parancsait](https://laravel.com/docs/5.4/migrations) az alkalmazás számára szükséges táblák létrehozásához. Ha látni szeretné a migrálások során létrejövő táblákat, tekintse meg a _database/migrations_ könyvtárat a Git-adattárban.

```bash
php artisan migrate
```

Hozzon létre egy új Laravel-alkalmazáskulcsot.

```bash
php artisan key:generate
```

Futtassa az alkalmazást.

```bash
php artisan serve
```

Egy böngészőben nyissa meg a `http://localhost:8000` oldalt. Vegyen fel néhány feladatot az oldalon.

![A PHP sikeresen csatlakozik a MySQL-hez](./media/tutorial-php-mysql-app/mysql-connect-success.png)

A PHP leállításához írja be a `Ctrl + C` billentyűparancsot a terminálon.

## <a name="create-mysql-in-azure"></a>MySQL létrehozása az Azure-ban

Ebben a lépésben egy MySQL-adatbázist hoz létre az [Azure Database for MySQL-ben](../mysql/index.yml). Később konfigurálni fogja a PHP-alkalmazást az adatbázishoz való csatlakozásra.

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-mysql-server"></a>MySQL-kiszolgáló létrehozása

A Cloud Shell hozzon létre egy kiszolgálót a Azure Database for MySQL a [`az mysql server create`](/cli/azure/mysql/server#az_mysql_server_create) paranccsal.

A következő parancsban helyettesítse be a helyőrzőt egy egyedi kiszolgálónévvel, a felhasználónévvel és a helyőrző *\<mysql-server-name>* *\<admin-user>* *\<admin-password>*  jelszavával. A kiszolgálónév a MySQL-végpont (`https://<mysql-server-name>.mysql.database.azure.com`) részét képezi majd, így egyedi kiszolgálónévnek kell lennie a teljes Azure-ban. A MySQL DB termékváltozat kiválasztásával kapcsolatos részletekért lásd: Create an Azure Database for MySQL server (Új kiszolgáló [létrehozása).](../mysql/quickstart-create-mysql-server-database-using-azure-cli.md#create-an-azure-database-for-mysql-server)

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name <mysql-server-name> --location "West Europe" --admin-user <admin-user> --admin-password <admin-password> --sku-name B_Gen5_1
```

A MySQL-kiszolgáló létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg:

<pre>
{
  "administratorLogin": "&lt;admin-user&gt;",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "&lt;mysql-server-name&gt;.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/&lt;mysql-server-name&gt;",
  "location": "westeurope",
  "name": "&lt;mysql-server-name&gt;",
  "resourceGroup": "myResourceGroup",
  ...
  -  &lt; Output has been truncated for readability &gt;
}
</pre>

### <a name="configure-server-firewall"></a>Kiszolgáló tűzfalának konfigurálása

A Cloud Shell hozzon létre egy tűzfalszabályt a MySQL-kiszolgálóhoz, hogy engedélyezze az ügyfélkapcsolatokat az [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_create) paranccsal. Ha a kezdő IP-cím és a záró IP-cím is 0.0.0.0 értékre van állítva, a tűzfal csak más Azure-erőforrások számára van nyitva. 

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql-server-name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> Még szigorúbb tűzfalszabályt is megadhat, ha [csak azokat a kimenő IP-címeket használja, amelyeket alkalmazása használ](overview-inbound-outbound-ips.md#find-outbound-ips).
>

A Cloud Shell futtassa újra a parancsot, hogy engedélyezze a helyi számítógéphez való hozzáférést a helyett a *\<your-ip-address>* [helyi IPv4 IP-címével.](https://www.whatsmyip.org/)

```azurecli-interactive
az mysql server firewall-rule create --name AllowLocalClient --server <mysql-server-name> --resource-group myResourceGroup --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address>
```

### <a name="connect-to-production-mysql-server-locally"></a>Helyi csatlakozás éles MySQL-kiszolgálóhoz

A helyi terminálablakban csatlakozzon az Azure-ban található MySQL-kiszolgálóhoz. Használja a korábban az _&lt; admin-user>_ és _&lt; a mysql-server-name>._ Amikor a rendszer jelszót kér, használja azt a jelszót, amelyet az adatbázis létrehozásakor adott meg az Azure-ban.

```bash
mysql -u <admin-user>@<mysql-server-name> -h <mysql-server-name>.mysql.database.azure.com -P 3306 -p
```

### <a name="create-a-production-database"></a>Éles adatbázis létrehozása

A `mysql` parancssorban hozzon létre egy adatbázist.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Engedélyekkel rendelkező felhasználó létrehozása

Hozzon létre egy _phpappuser_ nevű adatbázis-felhasználót, és adja meg neki az összes jogosultságot a `sampledb` adatbázisban. Az oktatóanyag egyszerűsége érdekében használja a _MySQLAzure2017 jelszót._

```sql
CREATE USER 'phpappuser' IDENTIFIED BY 'MySQLAzure2017'; 
GRANT ALL PRIVILEGES ON sampledb.* TO 'phpappuser';
```

Bontsa a kiszolgálókapcsolat a `quit` parancs beírásával.

```sql
quit
```

## <a name="connect-app-to-azure-mysql"></a>Az alkalmazás csatlakoztatása az Azure MySQL-hez

Ebben a lépésben csatlakoztatja a PHP-alkalmazást a MySQL-adatbázishoz, amelyet az Azure Database for MySQL-ben hozott létre.

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Az adatbázis-kapcsolat konfigurálása

Az adattár gyökérkönyvtárában, hozzon létre a _. env.production_ fájlt, és másolja bele a következő változókat. Cserélje le placeholder_ &lt; mysql-server-name nevet a>_ és *a DB_HOST* *DB_USERNAME.*

```
APP_ENV=production
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=<mysql-server-name>.mysql.database.azure.com
DB_DATABASE=sampledb
DB_USERNAME=phpappuser@<mysql-server-name>
DB_PASSWORD=MySQLAzure2017
MYSQL_SSL=true
```

Mentse a módosításokat.

> [!TIP]
> A MySQL-kapcsolati adatok védelme érdekében ez a fájl már ki van zárva a Git-adattárból (lásd: _.gitignore_ az adattár gyökérkönyvtárában). Később megtudhatja, hogyan konfigurálhat környezeti változókat az App Service-ben az Azure Database for MySQL-ben található adatbázishoz való csatlakozáshoz. A környezeti változók esetében nincs szüksége a *.env* fájlra az App Service-ben.
>

### <a name="configure-tlsssl-certificate"></a>TLS-/SSL-tanúsítvány konfigurálása

Alapértelmezés szerint a Azure Database for MySQL TLS-kapcsolatokat kényszerít ki az ügyfelektől. Az Azure-ban található MySQL-adatbázishoz való csatlakozáshoz használja a [_.pem_ tanúsítványt, amelyet az Azure Database for MySQL](../mysql/howto-configure-ssl.md) biztosít.

Nyissa meg a _config/database.php_ fájlt, majd adja hozzá az `sslmode` és az `options` paramétereket a `connections.mysql` szakaszhoz az alábbi kódban látható módon.

::: zone pivot="platform-windows"  

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/BaltimoreCyberTrustRoot.crt.pem', 
    ] : []
],
```

::: zone-end

::: zone pivot="platform-linux"

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL') && extension_loaded('pdo_mysql')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/BaltimoreCyberTrustRoot.crt.pem',
    ] : []
],
```

::: zone-end

Az oktatóanyagban az egyszerűség kedvéért a `BaltimoreCyberTrustRoot.crt.pem` tanúsítvány megtalálható az adattárban. 

### <a name="test-the-application-locally"></a>Az alkalmazás helyi tesztelése

Futtassa a Laravel adatbázis-migrálási parancsait az _.env.production_ fájllal környezeti fájlként az Azure Database for MySQL szolgáltatásban található MySQL-adatbázis tábláinak létrehozásához. Ne feledje, hogy a _.env.production_ fájl tartalmazza a kapcsolatadatokat az Azure-ban található MySQL-adatbázishoz való csatlakozáshoz.

```bash
php artisan migrate --env=production --force
```

A _. env.production_ még nem rendelkezik érvényes alkalmazáskulccsal. Hozzon létre számára egy újat a terminálon.

```bash
php artisan key:generate --env=production --force
```

Futtassa a mintaalkalmazást a _. env.production_ fájllal környezeti fájlként.

```bash
php artisan serve --env=production
```

Nyissa meg a `http://localhost:8000` címet. Ha az oldal hiba nélkül betölt, a PHP-alkalmazás csatlakozik az Azure-ban található MySQL-adatbázishoz.

Vegyen fel néhány feladatot az oldalon.

![A PHP sikeresen csatlakozik az Azure Database for MySQL-hez](./media/tutorial-php-mysql-app/mysql-connect-success.png)

A PHP leállításához írja be a `Ctrl + C` billentyűparancsot a terminálon.

### <a name="commit-your-changes"></a>A módosítások véglegesítése

A módosítások véglegesítéséhez futtassa az alábbi Git-parancsokat:

```bash
git add .
git commit -m "database.php updates"
```

Az alkalmazás készen áll az üzembe helyezésre.

## <a name="deploy-to-azure"></a>Üzembe helyezés az Azure-ban

Ebben a lépésben üzembe helyezi a MySQL-hez csatlakoztatott PHP-alkalmazást az Azure App Service-ben.

### <a name="configure-a-deployment-user"></a>Üzembe helyező felhasználó konfigurálása

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

::: zone pivot="platform-windows"  

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

::: zone-end

<a name="create"></a>
### <a name="create-a-web-app"></a>Webalkalmazás létrehozása

::: zone pivot="platform-windows"  

[!INCLUDE [Create web app no h](../../includes/app-service-web-create-web-app-php-no-h.md)] 

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-php-linux-no-h.md)] 

::: zone-end

### <a name="configure-database-settings"></a>Adatbázis-beállítások konfigurálása

Az App Service-ben a környezeti változókat _alkalmazásbeállításként_ adhatja meg az [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) paranccsal.

Az alábbi parancs a `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` és `DB_PASSWORD` alkalmazásbeállítást konfigurálja. Cserélje le a helyőrzők _&lt; alkalmazásnevét>_ _&lt; mysql-server-name helyőrzőket a>._

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DB_HOST="<mysql-server-name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="phpappuser@<mysql-server-name>" DB_PASSWORD="MySQLAzure2017" MYSQL_SSL="true"
```

A beállítások eléréséhez a PHP [getenv](https://www.php.net/manual/en/function.getenv.php) metódust használhatja. A Laravel-kód egy [env](https://laravel.com/docs/5.4/helpers#method-env) burkolót használ a PHP `getenv` helyett. A _config/database.php_ fájlban található MySQL-konfiguráció például az alábbi kódhoz hasonlít:

```php
'mysql' => [
    'driver'    => 'mysql',
    'host'      => env('DB_HOST', 'localhost'),
    'database'  => env('DB_DATABASE', 'forge'),
    'username'  => env('DB_USERNAME', 'forge'),
    'password'  => env('DB_PASSWORD', ''),
    ...
],
```

### <a name="configure-laravel-environment-variables"></a>Laravel környezeti változók konfigurálása

A Laravel egy alkalmazáskulcsot igényel az App Service-ben. Ezt alkalmazásbeállításokkal konfigurálhatja.

A helyi terminálablakban használja a `php artisan` parancsot egy új alkalmazáskulcs _.env_ fájlba való mentés nélküli létrehozásához.

```bash
php artisan key:generate --show
```

A Cloud Shell állítsa be az alkalmazáskulcsot a App Service alkalmazásban az [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) paranccsal. Cserélje le a helyőrzők _&lt; alkalmazásnevét>_ _&lt; az outputofphpartisankey:generate>._

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
```

`APP_DEBUG="true"` A arra utasítja a Laravelt, hogy adja vissza a hibakeresési információkat, amikor az üzembe helyezett alkalmazás hibákba ütközik. Éles alkalmazás futtatásakor állítsa át a biztonságosabb `false` értékűre.

### <a name="set-the-virtual-application-path"></a>A virtuális alkalmazáselérési út beállítása

::: zone pivot="platform-windows"  

Állítsa be az alkalmazás virtuális alkalmazásának elérési útját. Erre a lépésre azért van szükség, mert a [Laravel-alkalmazás életciklusa](https://laravel.com/docs/5.4/lifecycle) az alkalmazás gyökérkönyvtára helyett a _nyilvános_ könyvtárban kezdődik. A többi PHP-keretrendszer, amelynek az életciklusa a gyökérkönyvtárban kezdődik, a virtuális alkalmazáselérési út manuális konfigurálása nélkül is működik.

A Cloud Shell állítsa be a virtuális alkalmazás elérési útját az [`az resource update`](/cli/azure/resource#az_resource_update) paranccsal. Cserélje le _&lt; az alkalmazásnevet>_ helyőrzőre.

```azurecli-interactive
az resource update --name web --resource-group myResourceGroup --namespace Microsoft.Web --resource-type config --parent sites/<app_name> --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" --api-version 2015-06-01
```

Alapértelmezés szerint a Azure App Service a virtuális alkalmazás gyökérútvonalát ( ) az üzembe helyezett alkalmazásfájlok gyökérkönyvtárába _/_ _(sites\wwwroot ) mutat._

::: zone-end

::: zone pivot="platform-linux"

[A Laravel-alkalmazások életciklusa](https://laravel.com/docs/5.4/lifecycle) az alkalmazás gyökérkönyvtára helyett a nyilvános könyvtárban kezdődik.  Az App Service alapértelmezett PHP Docker-rendszerképe az Apache rendszert használja, és nem engedélyezi a `DocumentRoot` testreszabását a Laravel számára. A `.htaccess` használatával azonban újraírhatja az összes kérést úgy, hogy azok a _/public_ könyvtárra mutassanak a gyökérkönyvtár helyett. Az adattár gyökérkönyvtárában egy `.htaccess` már hozzá lett adva erre a célra. Ennek segítségével a Laravel-alkalmazás készen áll az üzembe helyezésre.

További információ: [Hely gyökerének módosítása.](configure-language-php.md#change-site-root)

::: zone-end

### <a name="push-to-azure-from-git"></a>Leküldéses üzenet küldése a Gitből az Azure-ra

::: zone pivot="platform-windows"  

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
&lt; Output has been truncated for readability &gt;
</pre>

> [!NOTE]
> Azt tapasztalhatja, hogy az üzembehelyezési folyamat [Composer](https://getcomposer.org/)-csomagokat telepít a folyamat végén. Az App Service nem futtatja ezeket az automatizálásokat az alapértelmezett üzembe helyezés során, ezért ez a mintaadattár három további fájllal rendelkezik a gyökérkönyvtárában ennek lehetővé tételéhez:
>
> - `.deployment` – Ez a fájl utasítja az App Service-t, hogy a `bash deploy.sh` fájlt futtassa egyéni üzembehelyezési szkriptként.
> - `deploy.sh` – Az egyéni üzembehelyezési szkript. Ha áttekinti a fájlt, láthatja, hogy a `php composer.phar install` és az `npm install` után fut.
> - `composer.phar` – A Composer csomagkezelője.
>
> Ezzel a módszerrel bármilyen lépést hozzáadhat a Git-alapú üzembe helyezéshez az App Service-ben. További információkért lásd: [Egyéni telepítési parancsfájl](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).
>

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
&lt; Output has been truncated for readability &gt;
</pre>

::: zone-end

### <a name="browse-to-the-azure-app"></a>Keresse meg az Azure-alkalmazást

Egy böngészőben keresse fel az `http://<app-name>.azurewebsites.net` címet, és vegyen fel néhány feladatot a listára.

:::image type="content" source="./media/tutorial-php-mysql-app/php-mysql-in-azure.png" alt-text="Képernyőkép az Új feladatok hozzáadása Feladatlista azure-alkalmazás példáról.":::

Gratulálunk, egy adatvezérelt PHP-alkalmazást futtat az Azure App Service-ben.

## <a name="update-model-locally-and-redeploy"></a>A modell helyileg történő frissítése és ismételt üzembe helyezése

Ebben a lépésben egy egyszerű módosítást fog elvégezni a `task` adatmodellben és a webalkalmazásban, majd közzéteszi a frissítést az Azure-ban.

A feladatokkal kapcsolatos forgatókönyvben úgy módosítja az alkalmazást, hogy befejezettként jelölhessen meg feladatokat.

### <a name="add-a-column"></a>Oszlop hozzáadása

A helyi terminálablakban keresse meg a Git-tárház gyökerét.

Hozzon létre egy új adatbázis-migrálást a `tasks` táblához:

```bash
php artisan make:migration add_complete_column --table=tasks
```

Ez a parancs megjeleníti a létrehozott migrálási fájl nevét. Keresse meg ezt a fájlt a _database/migrations_ könyvtárban, majd nyissa meg.

Cserélje le az `up` metódust az alábbi kódra:

```php
public function up()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->boolean('complete')->default(False);
    });
}
```

A fenti kód egy `complete` nevű logikai oszlopot ad hozzá a `tasks` táblában.

Cserélje le a `down` metódust az alábbi kódra a visszaállítási művelethez:

```php
public function down()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->dropColumn('complete');
    });
}
```

A módosítás helyi adatbázisban való végrehajtásához futtasson Laravel-adatbázis migrálásokat a helyi terminálablakban.

```bash
php artisan migrate
```

A [Laravel elnevezési szabály](https://laravel.com/docs/5.4/eloquent#defining-models) alapján a `Task` modell (lásd: _app/Task.php_) leképezése alapértelmezés szerint a `tasks` táblára történik.

### <a name="update-application-logic"></a>Az alkalmazáslogika frissítése

Nyissa meg a *routes/web.php* fájlt. Az alkalmazás ebben a fájlban definiálja az útvonalait és az üzleti logikáját.

A fájl végén adjon hozzá egy útvonalat az alábbi kóddal:

```php
/**
 * Toggle Task completeness
 */
Route::post('/task/{id}', function ($id) {
    error_log('INFO: post /task/'.$id);
    $task = Task::findOrFail($id);

    $task->complete = !$task->complete;
    $task->save();

    return redirect('/');
});
```

A fenti kód egy egyszerű frissítést hajt végre az adatmodellen a `complete` értékének átállításával.

### <a name="update-the-view"></a>A nézet frissítése

Nyissa meg a *resources/views/tasks.blade.php* fájlt. Keresse meg a nyitó `<tr>` címkét, és cserélje le a következőre:

```html
<tr class="{{ $task->complete ? 'success' : 'active' }}" >
```

A fenti kód módosítja a sor színét, attól függően, hogy a feladat befejeződött-e.

A következő sorban az alábbi kódot láthatja:

```html
<td class="table-text"><div>{{ $task->name }}</div></td>
```

Cserélje le a teljes sort az alábbi kódra:

```html
<td>
    <form action="{{ url('task/'.$task->id) }}" method="POST">
        {{ csrf_field() }}

        <button type="submit" class="btn btn-xs">
            <i class="fa {{$task->complete ? 'fa-check-square-o' : 'fa-square-o'}}"></i>
        </button>
        {{ $task->name }}
    </form>
</td>
```

A fenti kód hozzáadja a korábban megadott útvonalra hivatkozó elküldés gombot.

### <a name="test-the-changes-locally"></a>A módosítások helyi tesztelése

A helyi terminálablakban futtassa a fejlesztési kiszolgálót a Git-tárház gyökérkönyvtárából.

```bash
php artisan serve
```

A feladat állapotváltozásának megtekintéséhez nyissa meg a `http://localhost:8000` címet, és jelölje be a jelölőnégyzetet.

![A feladathoz hozzáadott jelölőnégyzet](./media/tutorial-php-mysql-app/complete-checkbox.png)

A PHP leállításához írja be a `Ctrl + C` billentyűparancsot a terminálon.

### <a name="publish-changes-to-azure"></a>Módosítások közzététele az Azure-ba

A módosítás Azure-adatbázisban való végrehajtásához futtasson Laravel-adatbázis migrálásokat a termelési kapcsolati sztringgel a helyi terminálablakban.

```bash
php artisan migrate --env=production --force
```

Véglegesítse az összes módosítást a Gitben, majd továbbítsa a kód módosításait az Azure-ba.

```bash
git add .
git commit -m "added complete checkbox"
git push azure main
```

Miután a `git push` befejeződött, lépjen az Azure-alkalmazáshoz, és tesztelje az új funkciót.

![Az Azure-ban közzétett modell- és adatbázis-módosítások](media/tutorial-php-mysql-app/complete-checkbox-published.png)

Ha felvett feladatokat, azok megmaradnak az adatbázisban. Az adatséma frissítései érintetlenül hagyják a meglévő adatokat.

## <a name="stream-diagnostic-logs"></a>Diagnosztikai naplók streamelése

::: zone pivot="platform-windows"  

Bár a PHP-alkalmazás az Azure App Service-ben fut, a konzolnaplófájlokat megkaphatja a terminálban is. Így ugyanazokat a diagnosztikai üzeneteket kaphatja meg az alkalmazáshibák elhárításához.

A naplóstreamelést a következő [`az webapp log tail`](/cli/azure/webapp/log#az_webapp_log_tail) paranccsal kezdheti meg: Cloud Shell.

```azurecli-interactive
az webapp log tail --name <app_name> --resource-group myResourceGroup
```

A naplóstreamelés elindulás után frissítse az Azure-alkalmazást a böngészőben, hogy némi webes forgalmat generálni tud. Ekkor láthatja, hogy a rendszer átadja a konzolnaplófájlokat a terminálnak. Ha nem jelennek meg azonnal a konzolnaplófájlok, ellenőrizze ismét 30 másodperc múlva.

A `Ctrl`+`C` billentyűparanccsal bármikor leállíthatja a naplóstreamelést.

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end

> [!TIP]
> A PHP-alkalmazások használhatják a normál [error_log()](https://php.net/manual/function.error-log.php) függvényt a kimenet konzolra küldéséhez. A mintaalkalmazás ezt a megközelítést alkalmazza az _app/Http/routes.php_ fájlban.
>
> Webes keretrendszerként a [Laravel a Monologot használja](https://laravel.com/docs/5.4/errors) naplózási szolgáltatóként. Ha szeretné megtudni, hogyan érheti el, hogy a Monolog-üzeneteket jelenítsen meg a konzolon, tekintse meg a [PHP: A Monolog használata a konzolba való naplózáshoz (php://out)](https://stackoverflow.com/questions/25787258/php-how-to-use-monolog-to-log-to-console-php-out) című cikket.
>
>

## <a name="manage-the-azure-app"></a>Az Azure-alkalmazás kezelése

A létrehozott [Azure Portal](https://portal.azure.com) az alkalmazás kezeléséhez.

A bal oldali menüben kattintson a **App Services** elemre, majd az Azure-alkalmazás nevére.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/tutorial-php-mysql-app/access-portal.png)

Látható az alkalmazás Áttekintés lapja. Itt elvégezhet olyan alapszintű felügyeleti feladatokat, mint a leállítás, elindítás, újraindítás, tallózás és törlés.

A bal oldali menü az alkalmazás konfigurálásához biztosít oldalakat.

![Az App Service lap az Azure Portalon](./media/tutorial-php-mysql-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * MySQL-adatbázis létrehozása az Azure-ban
> * PHP-alkalmazás csatlakoztatása a MySQL-hez
> * Az alkalmazás üzembe helyezése az Azure-ban
> * Az adatmodell frissítése és az alkalmazás ismételt üzembe helyezése
> * Diagnosztikai naplók streamelése az Azure-ból
> * Az alkalmazás kezelése az Azure Portalon

Lépjen a következő oktatóanyaghoz, amelyből megtudhatja, hogyan képezhet le egyedi DNS-nevet az alkalmazásokhoz.

> [!div class="nextstepaction"]
> [Oktatóanyag: Egyéni DNS-név leképezése az alkalmazásra](app-service-web-tutorial-custom-domain.md)

Vagy tekintse meg az egyéb erőforrásokat:

> [!div class="nextstepaction"]
> [PHP-alkalmazás konfigurálása](configure-language-php.md)

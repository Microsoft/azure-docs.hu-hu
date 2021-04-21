---
title: 'Oktatóanyag: PHP- (Laravel-) alkalmazás összeállítása rugalmas Azure Database for MySQL kiszolgálóval'
description: Ez az oktatóanyag azt ismerteti, hogyan lehet rugalmas kiszolgálóval PHP-alkalmazást építeni.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: tutorial
ms.devlang: php
ms.date: 9/21/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: bb38b72af6e7c649c0904c41d3052b15a4c36955
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770056"
---
# <a name="tutorial-build-a-php-laravel-and-mysql-flexible-server-preview-app-in-azure-app-service"></a>Oktatóanyag: PHP- (Laravel-) és rugalmas MySQL-kiszolgáló (előzetes verzió) alkalmazás Azure App Service


:::image type="content" source="media/tutorial-php-database-app/complete-checkbox-published.png" alt-text="PHP-webalkalmazás rugalmas kiszolgálóval az Azure-ban":::

[Azure App Service](../../app-service/overview.md) egy nagymértékben skálázható, önjavítást használó webes üzemeltetési szolgáltatást nyújt a Linux operációs rendszer használatával. Ez az oktatóanyag bemutatja, hogyan hozhat létre EGY PHP-alkalmazást az Azure-ban, és hogyan csatlakoztathatja azt egy MySQL-adatbázishoz. Ha elkészült, egy [Laravel-alkalmazás](https://laravel.com/) fog futni a Azure App Service on Linux.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * PHP- (Laravel-) alkalmazás beállítása helyi MySQL-lel
> * Rugalmas MySQL-kiszolgáló létrehozása (előzetes verzió)
> * PHP-alkalmazás csatlakoztatása rugalmas MySQL-kiszolgálóhoz (előzetes verzió)
> * Az alkalmazás üzembe helyezése Azure App Service
> * Az adatmodell frissítése és az alkalmazás ismételt üzembe helyezése
> * Az alkalmazás kezelése az Azure Portalon

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a kezdés előtt. [](../../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

1. [A Git telepítése](https://git-scm.com/)
2. [Telepítse a PHP 5.6.4-es vagy újabb verzióját](https://php.net/downloads.php)
3. [Telepítse a Composert](https://getcomposer.org/doc/00-intro.md)
4. Engedélyezze a következő PHP-bővítményeket, amelyre a Laravelnek szüksége van: OpenSSL, PDO-MySQL, Mbstring, Tokenizer, XML
5. [Telepítse és indítsa el a MySQL-t](https://dev.mysql.com/doc/refman/5.7/en/installing.html)

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

A terminálablakban lépjen egy üres könyvtárba, ahol klónozhatja a mintaalkalmazást.  Futtassa a következő parancsot a mintatárház klónozásához.

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

:::image type="content" source="media/tutorial-php-database-app/mysql-connect-success.png" alt-text="A PHP sikeresen csatlakozik a MySQL-hez":::

A PHP leállításához írja be a `Ctrl + C` billentyűparancsot a terminálon.

## <a name="create-a-mysql-flexible-server-preview"></a>Rugalmas MySQL-kiszolgáló létrehozása (előzetes verzió)
Ebben a lépésben létrehoz egy MySQL-adatbázist egy [Azure Database for MySQL](../index.yml) nyilvános előzetes verzióban elérhető rugalmas kiszolgálón. Később konfigurálni fogja a PHP-alkalmazást az adatbázishoz való csatlakozásra. A [Azure Cloud Shell](../../cloud-shell/overview.md)hozzon létre egy kiszolgálót a-ban az [`az flexible-server create`](/cli/azure/mysql/server#az_mysql_flexible_server_create) paranccsal.

```azurecli-interactive
az mysql flexible-server create  --resource-group myResourceGroup --public-access <IP-Address>
```

> [!IMPORTANT]
> - Jegyezze fel a **kiszolgálónevet** és a kapcsolati sztringet, hogy a következő lépésben a csatlakozáshoz és a laravel-adatáttelepítés futtatásához használja azt. 
> - Az **IP-Cím argumentumnál**  adja meg az ügyfélszámítógép IP-címét. A kiszolgáló a létrehozáskor zárolva van, és a kiszolgáló helyi kezeléséhez hozzáférést kell adnunk az ügyfélszámítógépnek.

### <a name="configure-server-firewall-to-allow-web-app-to-connect-to-the-server"></a>A kiszolgáló tűzfalának konfigurálása a webalkalmazás kiszolgálóhoz való csatlakozásának engedélyezése érdekében

A Cloud Shell hozzon létre egy tűzfalszabályt a MySQL-kiszolgálóhoz, hogy engedélyezze az ügyfélkapcsolatokat az az mysql server firewall-rule create paranccsal. Ha a kezdő IP-cím és a záró IP-cím is - a tűzfal csak olyan Azure-szolgáltatások számára van megnyitva, amelyek nem statikus IP-címmel csatlakoznak ```0.0.0.0``` a kiszolgálóhoz.

```azurecli
az mysql flexible-server firewall-rule create --name allanyAzureIPs --server <mysql-server-name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

### <a name="connect-to-production-mysql-server-locally"></a>Helyi csatlakozás éles MySQL-kiszolgálóhoz

A helyi terminálablakban csatlakozzon az Azure-ban található MySQL-kiszolgálóhoz. Használja a és a korábban megadott ```<admin-user>``` ```<mysql-server-name>``` értékét. Amikor a rendszer jelszót kér, használja azt a jelszót, amelyet az adatbázis létrehozásakor adott meg az Azure-ban.

```bash
mysql -u <admin-user> -h <mysql-server-name>.mysql.database.azure.com -P 3306 -p
```

### <a name="create-a-production-database"></a>Éles adatbázis létrehozása

A `mysql` parancssorban hozzon létre egy adatbázist.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Engedélyekkel rendelkező felhasználó létrehozása

Hozzon létre egy _phpappuser_ nevű adatbázis-felhasználót, és adja meg neki az összes jogosultságot a `sampledb` adatbázisban. Az oktatóanyag egyszerűsége érdekében használja a _MySQLAzure2020_ jelszót.

```sql
CREATE USER 'phpappuser' IDENTIFIED BY 'MySQLAzure2020';
GRANT ALL PRIVILEGES ON sampledb.* TO 'phpappuser';
```

Bontsa a kiszolgálókapcsolat a `quit` parancs beírásával.

```sql
quit
```

## <a name="connect-app-to-mysql-flexible-server"></a>Alkalmazás csatlakoztatása rugalmas MySQL-kiszolgálóhoz

Ebben a lépésben csatlakoztatja a PHP-alkalmazást a MySQL-adatbázishoz, amelyet az Azure Database for MySQL-ben hozott létre.

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Az adatbázis-kapcsolat konfigurálása

Az adattár gyökérkönyvtárában, hozzon létre a _. env.production_ fájlt, és másolja bele a következő változókat. Cserélje le a _&lt; mysql-server-name helyőrzőt a>_ és a *DB_HOST* *DB_USERNAME.*

```
APP_ENV=production
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=<mysql-server-name>.mysql.database.azure.com
DB_DATABASE=sampledb
DB_USERNAME=phpappuser
DB_PASSWORD=MySQLAzure2017
MYSQL_SSL=true
```

Mentse a módosításokat.

> [!TIP]
> A MySQL-kapcsolati adatok védelme érdekében ez a fájl már ki van zárva a Git-adattárból (lásd: _.gitignore_ az adattár gyökérkönyvtárában). Később megtudhatja, hogyan konfigurálhat környezeti változókat az App Service-ben az Azure Database for MySQL-ben található adatbázishoz való csatlakozáshoz. A környezeti változók esetében nincs szüksége a *.env* fájlra az App Service-ben.
>

### <a name="configure-tlsssl-certificate"></a>TLS-/SSL-tanúsítvány konfigurálása

Alapértelmezés szerint a rugalmas MySQL-kiszolgáló TLS-kapcsolatokat kényszerít ki az ügyfelektől. Az Azure-beli MySQL-adatbázishoz való csatlakozáshoz a rugalmas kiszolgáló által biztosított [ _.pem_ Azure Database for MySQL kell használnia.](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) Töltse [le ezt a tanúsítványt](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)), és helyezze el az **SSL** mappában a mintaalkalmazás adattárának helyi példányában.

Nyissa meg a _config/database.php_ fájlt, majd adja hozzá az `sslmode` és az `options` paramétereket a `connections.mysql` szakaszhoz az alábbi kódban látható módon.

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL') && extension_loaded('pdo_mysql')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/DigiCertGlobalRootCA.crt.pem',
    ] : []
],
```

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

:::image type="content" source="media/tutorial-php-database-app/mysql-connect-success.png" alt-text="A PHP sikeresen csatlakozik az Azure Database for MySQL-hez":::

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

Az FTP és a helyi Git üzembe helyezhető egy Azure-webalkalmazásban egy üzembe helyező felhasználó használatával. Miután konfigurálta az üzembe helyezési felhasználót, azt az összes Azure-beli üzemelő példányhoz használhatja. A fiókszintű üzembe helyezési felhasználónév és jelszó eltér az Azure-előfizetés hitelesítő adataitól.

Az üzembe helyezési felhasználó konfiguráláshoz futtassa [az az webapp deployment user set](/cli/azure/webapp/deployment/user#az_webapp_deployment_user_set) parancsot a Azure Cloud Shell. Cserélje _&lt; le>_ _&lt; felhasználónevet_ és jelszót>az üzembe helyezési felhasználó felhasználónevére és jelszavára.

A felhasználónévnek egyedinek kell lennie az Azure-ban, és a helyi Git-leküldések számára nem tartalmazhatja a "@" szimbólumot.
A jelszónak legalább nyolc karakter hosszúságúnak kell lennie, és a következő három elemből kettőnek kell lennie: betűk, számok és szimbólumok.

```bash
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku F1 --is-linux
```

A JSON-kimenet null értékűként jeleníti meg a jelszót. Ha ütközést kap. Részletek: 409-es hiba, módosítsa a felhasználónevet. Ha "Hibás kérés" kérést kap. Részletek: 400-as hiba, erősebb jelszót használjon. **Jegyezheti fel a webalkalmazások üzembe helyezéséhez használt felhasználónevét és jelszavát.**

### <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

A Cloud Shell hozzon létre egy App Service-tervet az erőforráscsoportban az [az appservice plan create paranccsal.](/cli/azure/appservice/plan#az_appservice_plan_create) Az alábbi példa létrehoz egy myAppServicePlan nevű App Service az Ingyenes tarifacsomagban (--sku F1) és egy Linux-tárolóban (--is-linux).

az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku F1 --is-linux

<a name="create"></a>

### <a name="create-a-web-app"></a>Webalkalmazás létrehozása

Hozzon létre [egy webalkalmazást](../../app-service/overview.md#app-service-on-linux) a myAppServicePlan App Service tervben.

A Cloud Shell az [az webapp create parancsot használhatja.](/cli/azure/webapp#az_webapp_create) A következő példában cserélje le az _&lt; app-name>_ egy globálisan egyedi alkalmazásnévre (érvényes karakterek: `a-z` , és `0-9` `-` ). A futtatókörnyezet beállítása `PHP|7.0` lett. Az összes támogatott futásidőt az [az webapp list-runtimes --linux](/cli/azure/webapp#az_webapp_list_runtimes)futtatásával láthatja.

```bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.3" --deployment-local-git
```

A webalkalmazás létrehozása után az Azure CLI az alábbi példához hasonló eredményeket jelenít meg:

```
Local git is configured with url of 'https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app-name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Ezzel létrehozott egy üres, új webalkalmazást, engedélyezett Git üzemelő példánnyal.

> [!NOTE]
> A távoli Git URL-címe megjelenik a deploymentLocalGitUrl tulajdonságban, https:// <username> @<alkalmazásnév>.scm.azurewebsites.net/<app-name>.git formátumban. Mentse ezt az URL-t, mert később még szüksége lesz rá.

### <a name="configure-database-settings"></a>Adatbázis-beállítások konfigurálása

Az App Service-ben a környezeti változókat _alkalmazásbeállításként_ adhatja meg az [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) paranccsal.

Az alábbi parancs a `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` és `DB_PASSWORD` alkalmazásbeállítást konfigurálja. Cserélje le az _&lt; alkalmazásnév_ helyőrzőit>_&lt; mysql-server-name helyőrzőket a>._

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DB_HOST="<mysql-server-name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="phpappuser" DB_PASSWORD="MySQLAzure2017" MYSQL_SSL="true"
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

A Cloud Shell állítsa be az alkalmazáskulcsot a App Service az [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) paranccsal. Cserélje le a helyőrzők _&lt; alkalmazásnevét>_ _&lt; az outputofphpartisankey:generate>._

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
```

`APP_DEBUG="true"` arra utasítja a Laravelt, hogy adja vissza a hibakeresési információkat, amikor az üzembe helyezett alkalmazás hibákat észlelt. Éles alkalmazás futtatásakor állítsa át a biztonságosabb `false` értékűre.

### <a name="set-the-virtual-application-path"></a>A virtuális alkalmazáselérési út beállítása

[A Laravel-alkalmazások életciklusa](https://laravel.com/docs/5.4/lifecycle) az alkalmazás gyökérkönyvtára helyett a nyilvános könyvtárban kezdődik.  Az App Service alapértelmezett PHP Docker-rendszerképe az Apache rendszert használja, és nem engedélyezi a `DocumentRoot` testreszabását a Laravel számára. A `.htaccess` használatával azonban újraírhatja az összes kérést úgy, hogy azok a _/public_ könyvtárra mutassanak a gyökérkönyvtár helyett. Az adattár gyökérkönyvtárában egy `.htaccess` már hozzá lett adva erre a célra. Ennek segítségével a Laravel-alkalmazás készen áll az üzembe helyezésre.

További információ: [Hely gyökerének módosítása.](../../app-service/configure-language-php.md?pivots=platform-linux#change-site-root)

### <a name="push-to-azure-from-git"></a>Leküldéses üzenet küldése a Gitből az Azure-ra

A helyi terminálablakba visszatérve adjon hozzá egy távoli Azure-mappát a helyi Git-adattárhoz. Cserélje _&lt; le a deploymentLocalGitUrl-from-create-step>_ helyére a távoli Git-fájl URL-címét, amit a Webalkalmazás létrehozása lépésben [mentett.](#create-a-web-app)

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

A távoli Azure-mappához történő küldéssel helyezze üzembe az alkalmazást a következő paranccsal. Amikor **a** Git Hitelesítőadat-kezelő hitelesítő adatokat kér, ügyeljen arra, hogy az üzembe helyezési felhasználó konfigurálásában létrehozott hitelesítő adatokat adja meg, ne pedig az üzembe helyezési kiszolgálóra való Azure Portal.

```bash
git push azure main
```

A parancs futtatása eltarthat néhány percig. Futtatás közben a parancs a következő példához hasonló információkat jelenít meg:

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

### <a name="browse-to-the-azure-app"></a>Keresse meg az Azure-alkalmazást

Egy böngészőben keresse fel az `http://<app-name>.azurewebsites.net` címet, és vegyen fel néhány feladatot a listára.

:::image type="content" source="media/tutorial-php-database-app/php-mysql-in-azure.png" alt-text="PHP-webalkalmazás az Azure-ban":::

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

:::image type="content" source="media/tutorial-php-database-app/complete-checkbox.png" alt-text="A feladathoz hozzáadott jelölőnégyzet":::

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

:::image type="content" source="media/tutorial-php-database-app/complete-checkbox-published.png" alt-text="Az Azure-ban közzétett modell- és adatbázis-módosítások":::

Ha felvett feladatokat, azok megmaradnak az adatbázisban. Az adatséma frissítései érintetlenül hagyják a meglévő adatokat.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Az előző lépésekben Azure-erőforrásokat hozott létre egy erőforráscsoportban. Ha várhatóan nem lesz szüksége ezekre az erőforrásokra a jövőben, törölje az erőforráscsoportot a következő parancs Cloud Shellben történő futtatásával:

```bash
az group delete --name myResourceGroup
```

<a name="next"></a>

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Erőforrások kezelése a Azure Portal](../../azure-resource-manager/management/manage-resources-portal.md) <br/>
> [!div class="nextstepaction"]
> [A kiszolgáló kezelése](how-to-manage-server-cli.md)

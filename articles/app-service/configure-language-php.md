---
title: PHP-alkalmazások konfigurálása
description: Megtudhatja, hogyan konfigurálhatja a PHP-alkalmazásokat a natív Windows-példányban vagy egy előre felépített PHP-tárolóban a Azure App Service. A cikk a leggyakoribb konfigurációs feladatokat ismerteti.
ms.devlang: php
ms.topic: article
ms.date: 06/02/2020
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: c40bc01553b9e848d668c0a699e9dcc9929f079e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779326"
---
# <a name="configure-a-php-app-for-azure-app-service"></a>PHP-alkalmazás konfigurálása Azure App Service

Ez az útmutató bemutatja, hogyan konfigurálhatja PHP-webalkalmazásait, mobil-háttéralkalmazásait és API-alkalmazásait a Azure App Service.

Ez az útmutató alapvető fogalmakat és utasításokat tartalmaz a PHP-fejlesztők számára, akik alkalmazásokat telepítnek a App Service. Ha még soha nem használta a Azure App Service, először kövesse a [PHP rövid](quickstart-php.md) útmutatóját és a PHP [with MySQL oktatóanyagot.](tutorial-php-mysql-app.md)

## <a name="show-php-version"></a>PHP-verzió megjelenítése

::: zone pivot="platform-windows"  

Az aktuális PHP-verzió futtatásához futtassa a következő parancsot a [Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query phpVersion
```

Az összes támogatott PHP-verzió futtatásához futtassa a következő parancsot a [Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp list-runtimes | grep php
```

::: zone-end

::: zone pivot="platform-linux"

Az aktuális PHP-verzió futtatásához futtassa a következő parancsot a [Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Az összes támogatott PHP-verzió futtatásához futtassa a következő parancsot a [Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

::: zone-end

## <a name="set-php-version"></a>PHP-verzió beállítása

::: zone pivot="platform-windows"  

Futtassa a következő parancsot a [Cloud Shell](https://shell.azure.com) a PHP 7.4-es verziójának beállítására:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --php-version 7.4
```

::: zone-end

::: zone pivot="platform-linux"

Futtassa a következő parancsot a [Cloud Shell](https://shell.azure.com) a PHP 7.2-es verziójának beállításhoz:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

::: zone-end

::: zone pivot="platform-windows"  

## <a name="run-composer"></a>A Composer futtatása

Ha szeretné App Service a [Composert](https://getcomposer.org/) az üzembe helyezéskor, a legegyszerűbb módszer, ha a Composert is beveszi az adattárba.

Egy helyi terminálablakban váltsa át a könyvtárat az adattár gyökérkönyvtárára, és kövesse a [Composer](https://getcomposer.org/download/) letöltésével kapcsolatos utasításokat a *composer.phar* fájlnak a könyvtár gyökerére való letöltéséhez.

Futtassa a következő parancsokat (telepítenie kell az [npm-et):](https://www.npmjs.com/get-npm)

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

Az adattár gyökerének két további fájlja van: *.deployment* és *deploy.sh.*

Nyissa *deploy.sh,* és keresse meg a következő `Deployment` szakaszt:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Adja hozzá a szükséges eszköz futtatásához szükséges kódszakaszt *a* szakasz `Deployment` végén:

```bash
# 4. Use composer
echo "$DEPLOYMENT_TARGET"
if [ -e "$DEPLOYMENT_TARGET/composer.json" ]; then
  echo "Found composer.json"
  pushd "$DEPLOYMENT_TARGET"
  php composer.phar install $COMPOSER_ARGS
  exitWithMessageOnError "Composer install failed"
  popd
fi
```

Véglegesítenie kell az összes módosítást, és üzembe kell helyeznie a kódot a Git vagy a Zip használatával, engedélyezett buildautomatizálással. A Composernek most már futnia kell az üzembe helyezés automatizálásának részeként.

## <a name="run-gruntbowergulp"></a>Run Fogt/Bower/Gulp

Ha azt szeretné, App Service népszerű automatizálási eszközöket futtatassanak az üzembe helyezéskor, például a Amit, a Bower vagy a Gulp eszközt, egyéni üzembe helyezési szkriptet [kell használnia.](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) App Service szkriptet a Git vagy a [Zip](deploy-zip.md) üzembe helyezésekor futtatja, ha engedélyezve van a buildautomatizálás. 

Ahhoz, hogy az adattár futtatni tudja ezeket az eszközöket, hozzá kell azokat adni apackage.js *függőségeihez.* Például:

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

Egy helyi terminálablakban váltsa át a könyvtárat az adattár gyökérkönyvtárára, és futtassa a következő parancsokat (telepítenie kell az [npm-et):](https://www.npmjs.com/get-npm)

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

Az adattár gyökérkönyvtárában most két további fájl található: *.deployment* és *deploy.sh.*

Nyissa *deploy.sh,* és keresse meg `Deployment` az alábbi szakaszt:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Ez a szakasz a futtatásával `npm install --production` végződik. Adja hozzá a szükséges eszköz futtatásához szükséges kódszakaszt *a* szakasz `Deployment` végén:

- [Bower](#bower)
- [Nyelő](#gulp)
- [Grunt](#grunt)

A [mintaalkalmazásban MEAN.js példát,](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135)amelyben az üzembe helyezési szkript egy egyéni parancsot `npm install` is futtat.

### <a name="bower"></a>Bower

Ez a kódrészlet a (kódrészlet) et `bower install` futtatja.

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Nyelő

Ez a kódrészlet a (kódrészlet) et `gulp imagemin` futtatja.

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Ez a kódrészlet a (kódrészlet) et `grunt` futtatja.

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

::: zone-end

::: zone pivot="platform-linux"

## <a name="customize-build-automation"></a>Buildautomatizálás testreszabása

Ha Git- vagy zip-csomagokkal, bekapcsolt buildautomatizálással telepíti az alkalmazást, a App Service az automatizálás lépéseit a következő lépéseken keresztül:

1. Futtatassa az egyéni szkriptet, ha az meg van `PRE_BUILD_SCRIPT_PATH` adva.
1. Futtassa az `php composer.phar install` parancsot.
1. Futtatassa az egyéni szkriptet, ha az meg van `POST_BUILD_SCRIPT_PATH` adva.

`PRE_BUILD_COMMAND` A `POST_BUILD_COMMAND` és a környezeti változók, amelyek alapértelmezés szerint üresek. A build előtti parancsok futtatásához definiálja a következőt: `PRE_BUILD_COMMAND` . A build utáni parancsok futtatásához definiálja a következőt: `POST_BUILD_COMMAND` .

Az alábbi példa két változót ad meg parancsok sorozatára, vesszővel elválasztva.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

A buildautomatizálás testreszabásához szükséges további környezeti változókért lásd: [Oryx-konfiguráció.](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)

A PHP-alkalmazások Linuxon való App Service és buildjével kapcsolatos további információkért lásd: [Oryx-dokumentáció: PHP-alkalmazások](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/php.md)észlelése és összeállítása.

## <a name="customize-start-up"></a>Indítás testreszabása

Alapértelmezés szerint a beépített PHP-tároló futtatja az Apache-kiszolgálót. Indításkor a futtatja a `apache2ctl -D FOREGROUND"` et. Ha szeretné, futtathat egy másik parancsot indításkor is, ha a következő parancsot futtatja a [Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

::: zone-end

## <a name="access-environment-variables"></a>Hozzáférés a környezeti változókhoz

Az App Service-szel az alkalmazás kódján kívül is [megadhatja az alkalmazások beállításait](configure-common.md#configure-app-settings). Ezután a standard [getenv() minta](https://secure.php.net/manual/function.getenv.php) használatával elérheti őket. Például egy `DB_HOST` nevű alkalmazásbeállítás hozzáféréséhez használja a következő kódot:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Hely gyökerének módosítása

::: zone pivot="platform-windows"  

A választott webes keretrendszer használhat alkönyvtárat a webhely gyökerének. A [Laravel például](https://laravel.com/)a *public/alkönyvtárat* használja webhelygyökerként.

A hely gyökerének testreszabásához állítsa be az alkalmazás virtuális alkalmazásának elérési útját az [`az resource update`](/cli/azure/resource#az_resource_update) paranccsal. A következő példa a webhely gyökerét az *adattár nyilvános/alkönyvtárára* állítja be. 

```azurecli-interactive
az resource update --name web --resource-group <group-name> --namespace Microsoft.Web --resource-type config --parent sites/<app-name> --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" --api-version 2015-06-01
```

Alapértelmezés szerint a Azure App Service a virtuális alkalmazás gyökérútvonalát ( ) az üzembe helyezett alkalmazásfájlok gyökérkönyvtárába _/_ _(sites\wwwroot ) mutat._

::: zone-end

::: zone pivot="platform-linux"

A választott webes keretrendszer használhat alkönyvtárat a webhely gyökerének. A [Laravel](https://laravel.com/)például a `public/` alkönyvtárat használja webhelygyökerként.

Az alapértelmezett PHP-App Service Apache-t használ, és nem szabhatja testre az alkalmazás webhelygyökerét. A korlátozást a következő tartalommal adjuk hozzá az adattár gyökerében található *.htaccess-fájlhoz:*

```
<IfModule mod_rewrite.c>
    RewriteEngine on
    RewriteCond %{REQUEST_URI} ^(.*)
    RewriteRule ^(.*)$ /public/$1 [NC,L,QSA]
</IfModule>
```

Ha inkább nem használná a *.htaccess* újraírást, üzembe helyezheti a Laravel-alkalmazását egy [egyéni Docker-rendszerképpel](quickstart-custom-container.md) is.

::: zone-end

## <a name="detect-https-session"></a>HTTPS-munkamenet észlelése

Ebben App Service esetben [az](https://wikipedia.org/wiki/TLS_termination_proxy) SSL-leépítés a hálózati terheléselosztásnál történik, így minden HTTPS-kérés titkosítatlan HTTP-kérésként éri el az alkalmazást. Ha az alkalmazáslogikának ellenőriznie kell, hogy a felhasználói kérések titkosítva vannak-e vagy sem, vizsgálja meg a `X-Forwarded-Proto` fejlécet.

```php
if (isset($_SERVER['HTTP_X_FORWARDED_PROTO']) && $_SERVER['HTTP_X_FORWARDED_PROTO'] === 'https') {
// Do something when HTTPS is used
}
```

A népszerű webes keretrendszerekkel a szokásos `X-Forwarded-*` alkalmazásmintában található információkhoz férhet hozzá. A [CodeIgniterben](https://codeigniter.com/)a [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) alapértelmezés szerint ellenőrzi a `X_FORWARDED_PROTO` értékét.

## <a name="customize-phpini-settings"></a>A php.ini testreszabása

Ha módosítania kell a PHP-telepítést, az alábbi lépésekkel bármelyikphp.ini [módosíthatja.](https://www.php.net/manual/ini.list.php)

> [!NOTE]
> A PHP-verzió és a jelenlegi konfigurációphp.ini *a* [phpinfo()](https://php.net/manual/function.phpinfo.php) meghívása az alkalmazásban.
>

### <a name="customize-non-php_ini_system-directives"></a><a name="Customize-non-PHP_INI_SYSTEM directives"></a>Nem PHP_INI_SYSTEM irányelvek testreszabása

::: zone pivot="platform-windows"  

A virtuális PHP_INI_USER, PHP_INI_PERDIR és PHP_INI_ALL (lásd aphp.ini [ irányelveket)](https://www.php.net/manual/ini.list.php)testreszabásához adjon hozzá egy fájlt az alkalmazás `.user.ini` gyökérkönyvtárához.

Adja hozzá a konfigurációs beállításokat a fájlhoz a fájlban használt `.user.ini` `php.ini` szintaxissal. Ha például be szeretné kapcsolni a beállítást, és 10 M-re szeretné állítani a beállítást, a fájl `display_errors` `upload_max_filesize` a következő szöveget `.user.ini` tartalmazná:

```
 ; Example Settings
 display_errors=On
 upload_max_filesize=10M

 ; Write errors to d:\home\LogFiles\php_errors.log
 ; log_errors=On
```

A módosításokkal újból üzembe kell indítani az alkalmazást, és újra kell indítani.

A fájlok használatának alternatívájaként a ini_set() használatával is testre szabhatja ezeket a nem PHP_INI_SYSTEM `.user.ini` irányelveket. [](https://www.php.net/manual/function.ini-set.php)

::: zone-end

::: zone pivot="platform-linux"

Az PHP_INI_USER, PHP_INI_PERDIR és PHP_INI_ALL-irányelvek (lásd aphp.ini [ irányelveket)](https://www.php.net/manual/ini.list.php)testreszabásához adjon hozzá egy *.htaccess* fájlt az alkalmazás gyökérkönyvtárához.

A *.htaccess fájlban* adja hozzá az irányelveket a szintaxis `php_value <directive-name> <value>` használatával. Például:

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

A módosításokkal újból üzembe kell indítani az alkalmazást, és újra kell indítani. Ha a Kuduval telepíti (például a [Git](deploy-local-git.md)használatával), az üzembe helyezés után automatikusan újraindul.

A *.htaccess* helyett a [ini_set()](https://www.php.net/manual/function.ini-set.php) használatával is testre szabhatja ezeket a nem PHP_INI_SYSTEM irányelveket.

::: zone-end

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>A PHP_INI_SYSTEM testreszabása

::: zone pivot="platform-windows"  

A .PHP_INI_SYSTEM (lásd [aphp.ini](https://www.php.net/manual/ini.list.php)irányelveket) testreszabásához nem használhatja a *.htaccess megközelítést.* App Service az alkalmazásbeállítással külön `PHP_INI_SCAN_DIR` mechanizmust biztosít.

Először futtassa a következő parancsot a [Cloud Shell](https://shell.azure.com) egy nevű alkalmazásbeállítás `PHP_INI_SCAN_DIR` hozzáadásához:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="d:\home\site\ini"
```

Nyissa meg a Kudu-konzolt ( `https://<app-name>.scm.azurewebsites.net/DebugConsole` ), és navigáljon a következőre: `d:\home\site` .

Hozzon létre egy nevű könyvtárat, majd hozzon létre egy .ini fájlt a könyvtárban (példáulsettings.ini) a testreszabni kívánt `d:\home\site` `ini`  `d:\home\site\ini` irányelvekkel.  Használja ugyanazt *a* szintaxist, mint aphp.inifájlban. 

Ha például módosítania kell a expose_php [a](https://php.net/manual/ini.core.php#ini.expose-php) következő parancsokat:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

A módosítások érvénybe lépnek, ha újraindítja az alkalmazást.

::: zone-end

::: zone pivot="platform-linux"

A .PHP_INI_SYSTEM (lásd [aphp.ini](https://www.php.net/manual/ini.list.php)irányelveket) testreszabásához nem használhatja a *.htaccess megközelítést.* App Service az alkalmazásbeállítással külön `PHP_INI_SCAN_DIR` mechanizmust biztosít.

Először futtassa a következő parancsot a [Cloud Shell](https://shell.azure.com) egy nevű alkalmazásbeállítás `PHP_INI_SCAN_DIR` hozzáadásához:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d` Az az alapértelmezett könyvtár, *php.ini* létezik. `/home/site/ini` Az az egyéni könyvtár, amelyben egyéni *.ini* fájlt fog hozzáadni. Az értékeket egy értékkel kell `:` elválasztani.

Nyissa meg a webes SSH-munkamenetet a Linux-tárolóval ( `https://<app-name>.scm.azurewebsites.net/webssh/host` ).

Hozzon létre egy nevű könyvtárat, majd hozzon létre egy .ini fájlt a könyvtárban (példáulsettings.ini) a testreszabni kívánt `/home/site` `ini`  `/home/site/ini` irányelvekkel.  Használja ugyanazt *a* szintaxist, mint aphp.inifájlban. 

> [!TIP]
> Az alkalmazás beépített Linux-tárolóiban a */home* App Service megőrzött megosztott tárolóként lesz használva. 
>

Ha például módosítania kell a expose_php [alábbi](https://php.net/manual/ini.core.php#ini.expose-php) parancsokat:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

A módosítások érvénybe lépnek, ha újraindítja az alkalmazást.

::: zone-end

## <a name="enable-php-extensions"></a>PHP-bővítmények engedélyezése

::: zone pivot="platform-windows"  

A beépített PHP-telepítések tartalmazzák a leggyakrabban használt bővítményeket. A további bővítményeket ugyanúgy engedélyezheti, mint a(php.ini [irányelveket.](#customize-php_ini_system-directives)

> [!NOTE]
> A PHP-verzió és a jelenlegi konfigurációphp.ini *a* [phpinfo()](https://php.net/manual/function.phpinfo.php) meghívása az alkalmazásban.
>

További bővítmények engedélyezéséhez kövesse az alábbi lépéseket:

Adjon hozzá egy könyvtárat az alkalmazás gyökérkönyvtárához, és helyezze bele a `bin` `.dll` *bővítményfájlokat (például* mongodb.dll). Győződjön meg arról, hogy a bővítmények kompatibilisek az Azure PHP-verziójával, valamint a VC9 és a nem szálmentes (nts) verzióval.

A módosítások üzembe helyezése.

Kövesse a PHP_INI_SYSTEM irányelveinek testreszabása [lépéseit,](#customize-php_ini_system-directives)és adja hozzá a [](https://www.php.net/manual/ini.core.php#ini.extension) bővítményeket az egyéni *.ini* fájlhoz a kiterjesztéssel vagy a zend_extension [irányelvekkel.](https://www.php.net/manual/ini.core.php#ini.zend-extension)

```
extension=d:\home\site\wwwroot\bin\mongodb.dll
zend_extension=d:\home\site\wwwroot\bin\xdebug.dll
```

A módosítások érvénybe lépnek, ha újraindítja az alkalmazást.

::: zone-end

::: zone pivot="platform-linux"

A beépített PHP-telepítések tartalmazzák a leggyakrabban használt bővítményeket. A további bővítményeket ugyanúgy engedélyezheti, mint a(php.ini [irányelveket.](#customize-php_ini_system-directives)

> [!NOTE]
> A PHP-verzió és a jelenlegi konfigurációphp.ini *a* [phpinfo()](https://php.net/manual/function.phpinfo.php) meghívása az alkalmazásban.
>

További bővítmények engedélyezéséhez kövesse az alábbi lépéseket:

Adjon hozzá egy könyvtárat az alkalmazás gyökérkönyvtárához, és helyezze bele a `bin` `.so` bővítményfájlokat *(például* mongodb.so). Győződjön meg arról, hogy a bővítmények kompatibilisek az Azure PHP-verziójával, valamint a VC9 és a nem szálmentes (nts) verzióval.

A módosítások üzembe helyezése.

Kövesse a PHP_INI_SYSTEM irányelveinek testreszabása [lépéseit,](#customize-php_ini_system-directives)és adja hozzá a [](https://www.php.net/manual/ini.core.php#ini.extension) bővítményeket az egyéni *.ini* fájlhoz a kiterjesztéssel vagy a zend_extension [irányelvekkel.](https://www.php.net/manual/ini.core.php#ini.zend-extension)

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

A módosítások érvénybe lépnek, ha újraindítja az alkalmazást.

::: zone-end

## <a name="access-diagnostic-logs"></a>Diagnosztikai naplók elérése

::: zone pivot="platform-windows"  

A standard [error_log()](https://php.net/manual/function.error-log.php) segédprogrammal készítse el a diagnosztikai naplókat a Azure App Service.

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

::: zone-end

## <a name="troubleshooting"></a>Hibaelhárítás

Ha egy működő PHP-alkalmazás másképp viselkedik a App Service vagy hibákat tartalmaz, próbálkozzon a következővel:

- [Hozzáférés a naplóstreamhez.](#access-diagnostic-logs)
- Az alkalmazás helyi tesztelése éles módban. App Service éles módban futtatja az alkalmazást, ezért győződjön meg arról, hogy a projekt a várt módon működik helyileg éles módban. Például:
    - Attól függően, *hogycomposer.jsvan-e,* különböző csomagok telepíthetők az éles módhoz ( és nem `require` `require-dev` ).
    - Bizonyos webes keretrendszerek eltérő módon helyezhetnek üzembe statikus fájlokat éles módban.
    - Bizonyos webes keretrendszerek egyéni indítási szkripteket használhatnak éles módban való futtatáskor.
- Futtassa az alkalmazást App Service módban. A [Laravelben](https://meanjs.org/)például úgy konfigurálhatja az alkalmazást, hogy éles környezetben is kiadja a hibakeresési üzeneteket, ha az alkalmazásbeállítást a következőre [ `APP_DEBUG` konfigurálja: `true` ](configure-common.md#configure-app-settings).

::: zone pivot="platform-linux"

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: PHP-alkalmazás és MySQL](tutorial-php-mysql-app.md)

::: zone pivot="platform-linux"

> [!div class="nextstepaction"]
> [App Service a Linuxon – gyakori kérdések](faq-app-service-linux.md)

::: zone-end

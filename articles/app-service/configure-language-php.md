---
title: PHP-alkalmazások konfigurálása
description: Megtudhatja, hogyan konfigurálhat egy PHP-alkalmazást a natív Windows-példányokban vagy egy előre elkészített PHP-tárolóban Azure App Serviceban. A cikk a leggyakoribb konfigurációs feladatokat ismerteti.
ms.devlang: php
ms.topic: article
ms.date: 06/02/2020
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: afac8273b5729bcf5470be471145214426dc7dab
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90055299"
---
# <a name="configure-a-php-app-for-azure-app-service"></a>PHP-alkalmazás konfigurálása Azure App Servicehoz

Ez az útmutató bemutatja, hogyan konfigurálhatja a PHP-webalkalmazásokat, a mobil háttér-és API-alkalmazásokat Azure App Serviceban.

Ez az útmutató a PHP-fejlesztőknek szóló alapfogalmakat és útmutatást tartalmaz, amelyek az alkalmazások App Service való telepítését végzik. Ha még soha nem használta a Azure App Servicet, először kövesse a [php](quickstart-php.md) gyors és a [php-t a MySQL-vel foglalkozó oktatóanyagban](tutorial-php-mysql-app.md) .

## <a name="show-php-version"></a>PHP-verzió megjelenítése

::: zone pivot="platform-windows"  

Az aktuális PHP-verzió megjelenítéséhez futtassa a következő parancsot a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query phpVersion
```

Az összes támogatott PHP-verzió megjelenítéséhez futtassa a következő parancsot a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes | grep php
```

::: zone-end

::: zone pivot="platform-linux"

Az aktuális PHP-verzió megjelenítéséhez futtassa a következő parancsot a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Az összes támogatott PHP-verzió megjelenítéséhez futtassa a következő parancsot a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

::: zone-end

## <a name="set-php-version"></a>PHP-verzió beállítása

::: zone pivot="platform-windows"  

Futtassa a következő parancsot a [Cloud Shell](https://shell.azure.com) a PHP-verzió 7,4-es értékre való beállításához:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --php-version 7.4
```

::: zone-end

::: zone pivot="platform-linux"

Futtassa a következő parancsot a [Cloud Shell](https://shell.azure.com) a PHP-verzió 7,2-es értékre való beállításához:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

::: zone-end

::: zone pivot="platform-windows"  

## <a name="run-composer"></a>Zeneszerző futtatása

Ha azt szeretné, hogy a App Service az üzembe helyezési időpontban futtassa a [zeneszerzőt](https://getcomposer.org/) , a legegyszerűbb módszer a zeneszerző belefoglalása a tárházba.

Egy helyi terminál ablakában váltson át a könyvtárra a tárház gyökerére, és kövesse a [zeneszerző](https://getcomposer.org/download/) *. farmakovigilancia* a könyvtár gyökeréhez való letöltéséhez szükséges utasításokat.

Futtassa a következő parancsokat (telepítenie kell a [NPM](https://www.npmjs.com/get-npm) ):

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

A tárház gyökerének most két további fájlja van: *. Deployment* és *Deploy.sh*.

Nyissa meg a *Deploy.sh* , és keresse meg az `Deployment` alábbihoz hasonló szakaszt:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Adja hozzá a Code *(kód)* szakaszt, amelyen futtatnia kell a szükséges eszközt a `Deployment` szakasz végén:

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

Véglegesítse az összes módosítást, és telepítse a kódot a git használatával vagy a zip üzembe helyezésével, ha a Build Automation engedélyezve van. A zeneszerzőnek most már futnia kell az üzembe helyezés automatizálásának részeként.

## <a name="run-gruntbowergulp"></a>Morog/Bower/Nyelő futtatása

Ha azt szeretné, hogy a App Service a népszerű Automation-eszközöket az üzembe helyezési időben, például a morog, a Bower vagy a Nyelő használatával futtassa, [egyéni telepítési parancsfájlt](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)kell megadnia. App Service futtatja ezt a parancsfájlt a git-vel való üzembe helyezéskor, vagy ha engedélyezve van a Build Automation szolgáltatással rendelkező [zip-telepítés](deploy-zip.md) . 

Ha engedélyezni szeretné, hogy a tárház futtassa ezeket az eszközöket, hozzá kell adnia azokat a függőségekhez *package.json.* Például:

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

Egy helyi terminál ablakban váltson át a tárház gyökerére, és futtassa a következő parancsokat (telepítenie kell a [NPM](https://www.npmjs.com/get-npm) -t):

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

A tárház gyökerének most két további fájlja van: *. Deployment* és *Deploy.sh*.

Nyissa meg a *Deploy.sh* , és keresse meg az `Deployment` alábbihoz hasonló szakaszt:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Ez a szakasz a futtatásával végződik `npm install --production` . Adja hozzá a Code *(kód)* szakaszt, amelyen futtatnia kell a szükséges eszközt a `Deployment` szakasz végén:

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

Tekintse [meg a MEAN.js minta példáját](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135), ahol az üzembe helyezési parancsfájl egy egyéni parancsot is futtat `npm install` .

### <a name="bower"></a>Bower

Ez a kódrészlet fut `bower install` .

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

Ez a kódrészlet fut `gulp imagemin` .

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Ez a kódrészlet fut `grunt` .

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

## <a name="customize-build-automation"></a>A Build Automation testreszabása

Ha a Build Automation használatával git vagy zip csomagok segítségével helyezi üzembe az alkalmazást, akkor a App Service az alábbi lépésekkel hozhat létre automatizálási lépéseket:

1. Futtassa az egyéni parancsfájlt, ha a meg van adva `PRE_BUILD_SCRIPT_PATH` .
1. Futtassa az `php composer.phar install` parancsot.
1. Futtassa az egyéni parancsfájlt, ha a meg van adva `POST_BUILD_SCRIPT_PATH` .

`PRE_BUILD_COMMAND``POST_BUILD_COMMAND`a és a környezeti változók, amelyek alapértelmezés szerint üresek. Az előkészítő parancsok futtatásához adja meg a következőt: `PRE_BUILD_COMMAND` . A létrehozás utáni parancsok futtatásához adja meg a következőt: `POST_BUILD_COMMAND` .

A következő példa a két változót adja meg egy több parancshoz, vesszővel elválasztva.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

További környezeti változók a Build Automation testreszabásához: [Oryx-konfiguráció](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

A PHP-alkalmazások Linux rendszeren való futtatásáról és App Serviceáról további információt a [Oryx dokumentációjában talál: a PHP-alkalmazások észlelése és építése](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/php.md).

## <a name="customize-start-up"></a>Indítás testreszabása

Alapértelmezés szerint a beépített PHP-tároló futtatja az Apache-kiszolgálót. Indításkor a fut `apache2ctl -D FOREGROUND"` . Ha szeretné, egy másik parancsot is futtathat az indításkor, ha a következő parancsot futtatja a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

::: zone-end

## <a name="access-environment-variables"></a>Hozzáférés a környezeti változókhoz

Az App Service-szel az alkalmazás kódján kívül is [megadhatja az alkalmazások beállításait](configure-common.md#configure-app-settings). Ezt követően a szabványos [GETENV ()](https://secure.php.net/manual/function.getenv.php) minta használatával érheti el őket. Például egy `DB_HOST` nevű alkalmazásbeállítás hozzáféréséhez használja a következő kódot:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Hely gyökerének módosítása

::: zone pivot="platform-windows"  

Az Ön által választott webes keretrendszer egy alkönyvtárt is használhat a hely gyökeréhez. Például a [Laravel](https://laravel.com/)a *nyilvános/* alkönyvtárat használja a hely gyökeréhez.

A hely gyökerének testreszabásához a parancs használatával állítsa be a virtuális alkalmazás elérési útját az alkalmazáshoz [`az resource update`](/cli/azure/resource#az-resource-update) . A következő példában a hely gyökerét a tárházban lévő *nyilvános/* alkönyvtárra állítja be. 

```azurecli-interactive
az resource update --name web --resource-group <group-name> --namespace Microsoft.Web --resource-type config --parent sites/<app-name> --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" --api-version 2015-06-01
```

Alapértelmezés szerint a Azure App Service a virtuális alkalmazás elérési útját ( _/_ ) a telepített alkalmazásfájlok (_sites\wwwroot_) gyökérkönyvtárára mutat.

::: zone-end

::: zone pivot="platform-linux"

Az Ön által választott webes keretrendszer egy alkönyvtárt is használhat a hely gyökeréhez. Például a [Laravel](https://laravel.com/)az `public/` alkönyvtárat használja a hely gyökeréhez.

A App Service alapértelmezett PHP-rendszerképe Apache-t használ, és nem teszi lehetővé a hely gyökerének testreszabását az alkalmazáshoz. A korlátozás megkerüléséhez adjon hozzá egy *. htaccess* -fájlt a tárház gyökeréhez a következő tartalommal:

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

App Service az [SSL-megszakítás](https://wikipedia.org/wiki/TLS_termination_proxy) a hálózati terheléselosztó esetében történik, így minden HTTPS-kérelem titkosítatlan http-kérésként éri el az alkalmazást. Ha az alkalmazás logikájának ellenőriznie kell, hogy a felhasználói kérések titkosítva vannak-e, vagy sem, vizsgálja meg a `X-Forwarded-Proto` fejlécet.

```php
if (isset($_SERVER['HTTP_X_FORWARDED_PROTO']) && $_SERVER['HTTP_X_FORWARDED_PROTO'] === 'https') {
// Do something when HTTPS is used
}
```

A népszerű webes keretrendszerek lehetővé teszik a `X-Forwarded-*` szabványos alkalmazási mintában lévő információk elérését. A [CodeIgniter](https://codeigniter.com/)-ben a [is_https ()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) alapértelmezés szerint ellenőrzi a értéket `X_FORWARDED_PROTO` .

## <a name="customize-phpini-settings"></a>php.ini beállítások testreszabása

Ha módosítania kell a PHP-telepítést, a következő lépésekkel módosíthatja a [php.ini irányelvek](https://www.php.net/manual/ini.list.php) bármelyikét.

> [!NOTE]
> A PHP-verzió és a jelenlegi *php.ini* konfiguráció megtekintésének legjobb módja az [phpinfo ()](https://php.net/manual/function.phpinfo.php) hívása az alkalmazásban.
>

### <a name="customize-non-php_ini_system-directives"></a><a name="Customize-non-PHP_INI_SYSTEM directives"></a>Testreszabás – nem PHP_INI_SYSTEM irányelvek

::: zone pivot="platform-windows"  

PHP_INI_USER, PHP_INI_PERDIR és PHP_INI_ALL irányelvek testreszabásához (lásd: [php.ini irányelvek](https://www.php.net/manual/ini.list.php)), adjon hozzá egy `.user.ini` fájlt az alkalmazás gyökérkönyvtárához.

Adja hozzá a konfigurációs beállításokat a `.user.ini` fájlhoz ugyanazzal a szintaxissal, amelyet egy `php.ini` fájlban szeretne használni. Ha például be szeretné kapcsolni a beállítást, és a `display_errors` `upload_max_filesize` beállítást 10 m-re állította, a `.user.ini` fájl a következő szöveget fogja tartalmazni:

```
 ; Example Settings
 display_errors=On
 upload_max_filesize=10M

 ; Write errors to d:\home\LogFiles\php_errors.log
 ; log_errors=On
```

Telepítse újra az alkalmazást a módosításokkal, majd indítsa újra.

Egy fájl használata helyett használhatja a `.user.ini` [ini_set ()](https://www.php.net/manual/function.ini-set.php) alkalmazást az alkalmazásban a nem PHP_INI_SYSTEM irányelvek testreszabásához.

::: zone-end

::: zone pivot="platform-linux"

PHP_INI_USER, PHP_INI_PERDIR és PHP_INI_ALL irányelvek testreszabásához (lásd: [php.ini irányelvek](https://www.php.net/manual/ini.list.php)), adjon hozzá egy *. htaccess* -fájlt az alkalmazás gyökérkönyvtárához.

A *. htaccess* fájlban adja hozzá az irányelveket a `php_value <directive-name> <value>` szintaxis használatával. Például:

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

Telepítse újra az alkalmazást a módosításokkal, majd indítsa újra. Ha a kudu (például a [git](deploy-local-git.md)használatával) telepíti azt, akkor a telepítés után automatikusan újraindul.

A *. htaccess* használatának alternatívájaként a [ini_set ()](https://www.php.net/manual/function.ini-set.php) alkalmazással is testreszabhatja ezeket a nem PHP_INI_SYSTEM irányelveket.

::: zone-end

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>PHP_INI_SYSTEM irányelvek testreszabása

::: zone pivot="platform-windows"  

PHP_INI_SYSTEM irányelvek testreszabásához (lásd: [php.ini irányelvek](https://www.php.net/manual/ini.list.php)) nem használhatja a *. htaccess* megközelítést. A App Service az Alkalmazásbeállítások használatával külön mechanizmust biztosít `PHP_INI_SCAN_DIR` .

Először futtassa az alábbi parancsot a [Cloud Shellban](https://shell.azure.com) egy, a következő nevű alkalmazás-beállítás hozzáadásához `PHP_INI_SCAN_DIR` :

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="d:\home\site\ini"
```

Navigáljon a kudu-konzolhoz ( `https://<app-name>.scm.azurewebsites.net/DebugConsole` ), és navigáljon a következőhöz: `d:\home\site` .

Hozzon létre egy könyvtárat a `d:\home\site` néven `ini` , majd hozzon létre egy *. ini* -fájlt a `d:\home\site\ini` könyvtárban (például *settings.ini)* a testreszabni kívánt irányelvek alapján. Használja ugyanazt a szintaxist, amelyet egy *php.ini* fájlban szeretne használni. 

A [expose_php](https://php.net/manual/ini.core.php#ini.expose-php) értékének módosításához például futtassa a következő parancsokat:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

A módosítások érvénybe léptetéséhez indítsa újra az alkalmazást.

::: zone-end

::: zone pivot="platform-linux"

PHP_INI_SYSTEM irányelvek testreszabásához (lásd: [php.ini irányelvek](https://www.php.net/manual/ini.list.php)) nem használhatja a *. htaccess* megközelítést. A App Service az Alkalmazásbeállítások használatával külön mechanizmust biztosít `PHP_INI_SCAN_DIR` .

Először futtassa az alábbi parancsot a [Cloud Shellban](https://shell.azure.com) egy, a következő nevű alkalmazás-beállítás hozzáadásához `PHP_INI_SCAN_DIR` :

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d` a *php.ini* létezésének alapértelmezett könyvtára. `/home/site/ini` az az egyéni könyvtár, amelyben hozzá kell adnia egy egyéni *. ini* -fájlt. Az értékeket az a értékkel válassza el `:` .

Navigáljon a web SSH-munkamenethez a Linux-tárolóval ( `https://<app-name>.scm.azurewebsites.net/webssh/host` ).

Hozzon létre egy könyvtárat a `/home/site` néven `ini` , majd hozzon létre egy *. ini* -fájlt a `/home/site/ini` könyvtárban (például *settings.ini)* a testreszabni kívánt irányelvek alapján. Használja ugyanazt a szintaxist, amelyet egy *php.ini* fájlban szeretne használni. 

> [!TIP]
> A App Service beépített Linux-tárolókban a */Home* a megőrzött megosztott tárolóként szolgál. 
>

A [expose_php](https://php.net/manual/ini.core.php#ini.expose-php) értékének módosításához például futtassa a következő parancsokat:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

A módosítások érvénybe léptetéséhez indítsa újra az alkalmazást.

::: zone-end

## <a name="enable-php-extensions"></a>PHP-bővítmények engedélyezése

::: zone pivot="platform-windows"  

A beépített PHP-telepítések a leggyakrabban használt bővítményeket tartalmazzák. A További bővítmények ugyanúgy engedélyezhetők, mint az [php.ini irányelvek testreszabása](#customize-php_ini_system-directives).

> [!NOTE]
> A PHP-verzió és a jelenlegi *php.ini* konfiguráció megtekintésének legjobb módja az [phpinfo ()](https://php.net/manual/function.phpinfo.php) hívása az alkalmazásban.
>

További bővítmények engedélyezéséhez kövesse az alábbi lépéseket:

Adjon hozzá egy `bin` könyvtárat az alkalmazás gyökérkönyvtárához, és helyezze a `.dll` bővítmény fájljait (például *mongodb.dll*). Győződjön meg arról, hogy a bővítmények kompatibilisek az Azure PHP-verziójával, és VC9 és nem szálon (Zem) kompatibilisek.

Telepítse a módosításokat.

Kövesse az [PHP_INI_SYSTEM irányelvek testreszabása](#customize-php_ini_system-directives)című szakasz lépéseit, adja hozzá a bővítményeket az egyéni *. ini* -fájlhoz a [kiterjesztéssel](https://www.php.net/manual/ini.core.php#ini.extension) vagy [zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension) irányelvekkel.

```
extension=d:\home\site\wwwroot\bin\mongodb.dll
zend_extension=d:\home\site\wwwroot\bin\xdebug.dll
```

A módosítások érvénybe léptetéséhez indítsa újra az alkalmazást.

::: zone-end

::: zone pivot="platform-linux"

A beépített PHP-telepítések a leggyakrabban használt bővítményeket tartalmazzák. A További bővítmények ugyanúgy engedélyezhetők, mint az [php.ini irányelvek testreszabása](#customize-php_ini_system-directives).

> [!NOTE]
> A PHP-verzió és a jelenlegi *php.ini* konfiguráció megtekintésének legjobb módja az [phpinfo ()](https://php.net/manual/function.phpinfo.php) hívása az alkalmazásban.
>

További bővítmények engedélyezéséhez kövesse az alábbi lépéseket:

Adjon hozzá egy `bin` könyvtárat az alkalmazás gyökérkönyvtárához, és helyezze a `.so` bővítmény fájljait (például *mongodb.so*). Győződjön meg arról, hogy a bővítmények kompatibilisek az Azure PHP-verziójával, és VC9 és nem szálon (Zem) kompatibilisek.

Telepítse a módosításokat.

Kövesse az [PHP_INI_SYSTEM irányelvek testreszabása](#customize-php_ini_system-directives)című szakasz lépéseit, adja hozzá a bővítményeket az egyéni *. ini* -fájlhoz a [kiterjesztéssel](https://www.php.net/manual/ini.core.php#ini.extension) vagy [zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension) irányelvekkel.

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

A módosítások érvénybe léptetéséhez indítsa újra az alkalmazást.

::: zone-end

## <a name="access-diagnostic-logs"></a>Diagnosztikai naplók elérése

::: zone pivot="platform-windows"  

A standard [error_log ()](https://php.net/manual/function.error-log.php) segédprogrammal megtekintheti a diagnosztikai naplókat a Azure app Serviceban.

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

::: zone-end

## <a name="troubleshooting"></a>Hibaelhárítás

Ha egy működő PHP-alkalmazás másképp viselkedik App Service vagy hibákat tartalmaz, próbálkozzon a következőkkel:

- [A log stream elérése](#access-diagnostic-logs).
- Az alkalmazás helyi tesztelése éles módban. App Service éles módban futtatja az alkalmazást, ezért meg kell győződnie arról, hogy a projekt a várt módon működik a helyi üzemi módban. Például:
    - A *composer.jstól* függően különböző csomagok is telepíthetők üzemi módba (vagy `require` `require-dev` ).
    - Bizonyos webes keretrendszerek eltérő üzemi módban telepíthetnek statikus fájlokat.
    - Bizonyos webes keretrendszerek éles módban történő futtatáskor egyéni indítási parancsfájlokat is használhatnak.
- Az alkalmazást hibakeresési módban App Service futtathatja. A [Laravel](https://meanjs.org/)-ben például beállíthatja, hogy az alkalmazás a hibakeresési üzeneteket az éles környezetben állítsa be úgy, hogy [az `APP_DEBUG` `true` alkalmazás beállítását ](configure-common.md#configure-app-settings)adja meg.

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


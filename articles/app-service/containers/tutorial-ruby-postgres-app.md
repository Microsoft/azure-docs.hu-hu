---
title: 'Oktatóanyag: Linux Ruby-alkalmazás és postgres'
description: Ismerje meg, hogyan szerezhet be egy, az Azure-ban egy PostgreSQL-adatbázishoz csatlakozó Linux Ruby-alkalmazást Azure App Serviceban. A Rails használatban van az oktatóanyagban.
ms.devlang: ruby
ms.topic: tutorial
ms.date: 03/27/2019
ms.custom: mvc, cli-validate, seodec18
ms.openlocfilehash: 2bc30786ccd0bccfba438fa6e553fdcbbf7fdde1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82085781"
---
# <a name="build-a-ruby-and-postgres-app-in-azure-app-service-on-linux"></a>Ruby-és postgres-alkalmazás létrehozása Linuxon Azure App Service

A [linuxon app Service](app-service-linux-intro.md) a Linux operációs rendszert használó, jól méretezhető, önjavító webes üzemeltetési szolgáltatást nyújt. Ez az oktatóanyag bemutatja, hogyan hozható létre Ruby-alkalmazás, és hogyan csatlakoztatható egy PostgreSQL-adatbázishoz. Az oktatóanyag eredménye egy, a Linux App Service-ben futó [Ruby on Rails](https://rubyonrails.org/)-alkalmazás lesz.

![Az Azure App Service-ben futó Ruby on Rails-alkalmazás](./media/tutorial-ruby-postgres-app/complete-checkbox-published.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * PostgreSQL-adatbázis létrehozása az Azure-ban
> * Ruby on Rails-alkalmazás csatlakoztatása a PostgreSQL-hez
> * Az alkalmazás üzembe helyezése az Azure-ban
> * Az adatmodell frissítése és az alkalmazás ismételt üzembe helyezése
> * Diagnosztikai naplók streamelése az Azure-ból
> * Az alkalmazás kezelése az Azure Portalon

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* [A Git telepítése](https://git-scm.com/)
* [Telepítse a Ruby 2.3-as verzióját](https://www.ruby-lang.org/en/documentation/installation/)
* [Telepítse a Ruby on Rails 5.1-es verzióját](https://guides.rubyonrails.org/v5.1/getting_started.html)
* [A PostgreSQL telepítése és futtatása](https://www.postgresql.org/download/)

## <a name="prepare-local-postgres"></a>A helyi Postgres előkészítése

Ebben a lépésben egy adatbázist hoz létre a helyi Postgres-kiszolgálón, hogy felhasználhassa azt az oktatóanyagban.

### <a name="connect-to-local-postgres-server"></a>Csatlakozás a helyi Postgres-kiszolgálóhoz

A helyi Postgres-kiszolgálóhoz való csatlakozáshoz nyissa meg a terminálablakot, és futtassa a `psql` parancsot.

```bash
sudo -u postgres psql
```

Ha a kapcsolat létrejött, a Postgres-adatbázis fut. Ha nem, mindenképp a [Letöltések – PostgreSQL központi kiadással](https://www.postgresql.org/download/) foglalkozó szakaszban ismertetett lépéseket követve indítsa el a helyi Postgres-adatbázist.

A Postgres-ügyfél bezárásához írja be a `\q` parancsot. 

A következő parancs futtatásával és bejelentkezett Linux-felhasználónevének használatával hozzon létre egy adatbázisok létrehozására képes Postgres-felhasználót.

```bash
sudo -u postgres createuser -d <signed-in-user>
```

<a name="step2"></a>

## <a name="create-a-ruby-on-rails-app-locally"></a>Ruby on Rails-alkalmazás helyi létrehozása
Ebben a lépésben egy Ruby on Rails-mintaalkalmazásra tesz szert, konfigurálja annak adatbázis-kapcsolatát, és helyileg futtatja az alkalmazást. 

### <a name="clone-the-sample"></a>A minta klónozása

A terminálablakban a `cd` paranccsal lépjen egy munkakönyvtárra.

Futtassa a következő parancsot a mintatárház klónozásához.

```bash
git clone https://github.com/Azure-Samples/rubyrails-tasks.git
```

A `cd` paranccsal lépjen a klónozott könyvtárra. Telepítse a szükséges csomagokat.

```bash
cd rubyrails-tasks
bundle install --path vendor/bundle
```

### <a name="run-the-sample-locally"></a>A minta futtatása helyben

Futtassa [a Rails migrálásokat](https://guides.rubyonrails.org/active_record_migrations.html#running-migrations) az alkalmazás számára szükséges táblák létrehozásához. Ha látni szeretné a migrálások során keletkező táblákat, tekintse meg a _db/migrate_ könyvtárat a Git-tárházban.

```bash
rake db:create
rake db:migrate
```

Futtassa az alkalmazást.

```bash
rails server
```

Egy böngészőben nyissa meg a `http://localhost:3000` oldalt. Vegyen fel néhány feladatot az oldalon.

![A Ruby on Rails sikeresen csatlakozik a Postgreshez](./media/tutorial-ruby-postgres-app/postgres-connect-success.png)

A Rails-kiszolgáló leállításához írja be a `Ctrl + C` billentyűparancsot a terminálon.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-postgres-in-azure"></a>Postgres létrehozása az Azure-ban

Ebben a lépésben egy Postgres-adatbázist fog létrehozni az [Azure Database for PostgreSQL](/azure/postgresql/) rendszerében. Később konfigurálni fogja a Ruby on Rails-alkalmazást az adatbázishoz való csatlakozásra.

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-a-postgres-server"></a>Postgres-kiszolgáló létrehozása

Hozzon létre egy PostgreSQL- [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) kiszolgálót a paranccsal.

Futtassa a következő parancsot a Cloud Shellban, és helyettesítse be a * \<postgres-Server-Name>* helyőrző egyedi kiszolgálójának nevét. A kiszolgálónév az összes Azure-kiszolgálón csak egyszer fordulhat elő. 

```azurecli-interactive
az postgres server create --location "West Europe" --resource-group myResourceGroup --name <postgres-server-name> --admin-user adminuser --admin-password My5up3r$tr0ngPa$w0rd! --sku-name GP_Gen4_2
```

Az Azure Database for PostgreSQL-kiszolgáló létrehozását követően az Azure CLI az alábbi példához hasonló információkat jelenít meg:

<pre>
{
  "administratorLogin": "adminuser",
  "earliestRestoreDate": "2018-06-15T12:38:25.280000+00:00",
  "fullyQualifiedDomainName": "&lt;postgres-server-name&gt;.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/&lt;postgres-server-name&gt;",
  "location": "westeurope",
  "name": "&lt;postgres-server-name&gt;",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": 2,
    "family": "Gen4",
    "name": "GP_Gen4_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  &lt; Output has been truncated for readability &gt;
}
</pre>

### <a name="configure-server-firewall"></a>Kiszolgáló tűzfalának konfigurálása

A Cloud Shell hozzon létre egy tűzfalszabály a postgres-kiszolgálóhoz, hogy engedélyezze az ügyfélkapcsolatokat a [`az postgres server firewall-rule create`](/cli/azure/postgres/server/firewall-rule?view=azure-cli-latest#az-postgres-server-firewall-rule-create) parancs használatával. Ha a kezdő IP-cím és a záró IP-cím is 0.0.0.0 értékre van állítva, a tűzfal csak más Azure-erőforrások számára van nyitva. Helyettesítse be a * \<postgres-Server-Name>* helyőrző egyedi kiszolgálójának nevét.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server <postgres-server-name> --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> Még szigorúbb tűzfalszabályt is megadhat, ha [csak azokat a kimenő IP-címeket használja, amelyeket alkalmazása használ](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).
>

### <a name="connect-to-production-postgres-server-locally"></a>Helyi csatlakozás éles Postgres-kiszolgálóhoz

A Cloud Shellben csatlakozzon az Azure-beli Postgres-kiszolgálóhoz. Használja a korábban megadott értéket a _ &lt;postgres-Server-Name>_ helyőrzők számára.

```bash
psql -U adminuser@<postgres-server-name> -h <postgres-server-name>.postgres.database.azure.com postgres
```

Amikor a rendszer jelszót kér, használja a _My5up3r$tr0ngPa$w0rd!_ jelszót, amelyet az adatbázis-kiszolgáló létrehozásakor adott meg.

### <a name="create-a-production-database"></a>Éles adatbázis létrehozása

A `postgres` parancssorban hozzon létre egy adatbázist.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Engedélyekkel rendelkező felhasználó létrehozása

Hozzon létre egy _railsappuser_ nevű adatbázis-felhasználót, és adja meg neki az összes jogosultságot a `sampledb` adatbázisban.

```sql
CREATE USER railsappuser WITH PASSWORD 'MyPostgresAzure2017';
GRANT ALL PRIVILEGES ON DATABASE sampledb TO railsappuser;
```

Bontsa a kiszolgálókapcsolat a `\q` parancs beírásával.

## <a name="connect-app-to-azure-postgres"></a>Az alkalmazás csatlakoztatása az Azure Postgreshez

Ebben a lépésben csatlakoztatni fogja a Ruby on Rails-alkalmazást a Postgres-adatbázishoz, amelyet az Azure Database for PostgreSQL szolgáltatásban hozott létre.

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Az adatbázis-kapcsolat konfigurálása

A tárházban nyissa meg a _config/database.yml_ fájlt. A fájl végén cserélje le a termelési változókat az alábbi kódra. 

```txt
production:
  <<: *default
  host: <%= ENV['DB_HOST'] %>
  database: <%= ENV['DB_DATABASE'] %>
  username: <%= ENV['DB_USERNAME'] %>
  password: <%= ENV['DB_PASSWORD'] %>
```

Mentse a módosításokat.

### <a name="test-the-application-locally"></a>Az alkalmazás helyi tesztelése

A helyi terminálban állítsa be a következő környezeti változókat:

```bash
export DB_HOST=<postgres-server-name>.postgres.database.azure.com
export DB_DATABASE=sampledb 
export DB_USERNAME=railsappuser@<postgres-server-name>
export DB_PASSWORD=MyPostgresAzure2017
```

Futtasson Rails-adatbázis migrálásokat az imént beállított termelési értékekkel a táblák létrehozásához az Azure Database for PostgreSQL szolgáltatás Postgres-adatbázisában.

```bash
rake db:migrate RAILS_ENV=production
```

Éles környezetben történő futtatásakor a Rails-alkalmazásnak előre lefordított objektumokra van szüksége. Hozza létre a szükséges objektumokat a következő paranccsal:

```bash
rake assets:precompile
```

A Rails éles környezet titkos kulcsokat is használ a biztonság kezeléséhez. Hozzon létre egy titkos kulcsot.

```bash
rails secret
```

Mentse a titkos kulcsot a Rails éles környezet által használt megfelelő változókba. Az egyszerűség kedvéért használja ugyanazt a kulcsot mindkét változóhoz.

```bash
export RAILS_MASTER_KEY=<output-of-rails-secret>
export SECRET_KEY_BASE=<output-of-rails-secret>
```

Engedélyezze a Rails éles környezet számára a JavaScript- és CSS-fájlok kiszolgálását.

```bash
export RAILS_SERVE_STATIC_FILES=true
```

Futtassa a mintaalkalmazást az éles környezetben.

```bash
rails server -e production
```

Nyissa meg a `http://localhost:3000` címet. Ha az oldal hiba nélkül betölt, a Ruby on Rails-alkalmazás csatlakozik az Azure-ban található Postgres-adatbázishoz.

Vegyen fel néhány feladatot az oldalon.

![A Ruby on Rails sikeresen csatlakozik az Azure Database for PostgreSQL-hez](./media/tutorial-ruby-postgres-app/azure-postgres-connect-success.png)

A Rails-kiszolgáló leállításához írja be a `Ctrl + C` billentyűparancsot a terminálon.

### <a name="commit-your-changes"></a>A módosítások véglegesítése

A módosítások véglegesítéséhez futtassa az alábbi Git-parancsokat:

```bash
git add .
git commit -m "database.yml updates"
```

Az alkalmazás készen áll az üzembe helyezésre.

## <a name="deploy-to-azure"></a>Üzembe helyezés az Azure-ban

Ebben a lépésben üzembe helyezi a Postgreshez csatlakoztatott Rails-alkalmazást az Azure App Service-ben.

### <a name="configure-a-deployment-user"></a>Üzembe helyező felhasználó konfigurálása

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

[!INCLUDE [Create app service plan no h](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Webalkalmazás létrehozása

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

### <a name="configure-database-settings"></a>Adatbázis-beállítások konfigurálása

Az App Service-ben a környezeti változókat _alkalmazásbeállításként_ lehet beállítani az [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) parancs Cloud Shellben való használatával.

Az alábbi Cloud Shell-parancs a `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` és `DB_PASSWORD` alkalmazásbeállításokat konfigurálja. Cserélje le a helyőrzőket a>és _ &lt;a postgres-Server-Name>_ _ &lt;AppName_ .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DB_HOST="<postgres-server-name>.postgres.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="railsappuser@<postgres-server-name>" DB_PASSWORD="MyPostgresAzure2017"
```

### <a name="configure-rails-environment-variables"></a>Rails környezeti változók konfigurálása

A helyi terminálon [egy új titkot állít elő](configure-language-ruby.md#set-secret_key_base-manually) a Rails éles környezet számára az Azure-ban.

```bash
rails secret
```

Konfigurálja a Rails éles környezet számára szükséges változókat.

A következő Cloud Shell parancsban cserélje le a két _ &lt;output-of-Rails-Secret>_ helyőrzőt a helyi terminálon létrehozott új titkos kulccsal.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings RAILS_MASTER_KEY="<output-of-rails-secret>" SECRET_KEY_BASE="<output-of-rails-secret>" RAILS_SERVE_STATIC_FILES="true" ASSETS_PRECOMPILE="true"
```

Az `ASSETS_PRECOMPILE="true"` arra utasítja az alapértelmezett Ruby tárolót, hogy minden Git-üzembehelyezésnél fordítson elő objektumokat. További információ: [eszközök előfordítása](configure-language-ruby.md#precompile-assets) és [statikus eszközök kiszolgálása](configure-language-ruby.md#serve-static-assets).

### <a name="push-to-azure-from-git"></a>Leküldéses üzenet küldése a Gitből az Azure-ra

A helyi terminálban adjon hozzá egy távoli Azure-mappát a helyi Git-tárházhoz.

```bash
git remote add azure <paste-copied-url-here>
```

A távoli Azure-mappához történő küldéssel helyezze üzembe a Ruby on Rails-alkalmazást. Az üzembe helyező felhasználó létrehozásának részeként a rendszer felkéri a korábban megadott jelszó megadására.

```bash
git push azure master
```

Az üzembe helyezés során az Azure App Service közli előrehaladási állapotát a Gittel.

<pre>
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
&lt; Output has been truncated for readability &gt;
</pre>

### <a name="browse-to-the-azure-app"></a>Tallózással keresse meg az Azure-alkalmazást

Egy böngészőben keresse fel az `http://<app-name>.azurewebsites.net` címet, és vegyen fel néhány feladatot a listára.

![Az Azure App Service-ben futó Ruby on Rails-alkalmazás](./media/tutorial-ruby-postgres-app/ruby-postgres-in-azure.png)

Gratulálunk, egy adatvezérelt Ruby on Rails-alkalmazást futtat az Azure App Service-ben.

## <a name="update-model-locally-and-redeploy"></a>A modell helyileg történő frissítése és ismételt üzembe helyezése

Ebben a lépésben egy egyszerű módosítást fog elvégezni a `task` adatmodellben és a webalkalmazásban, majd közzéteszi a frissítést az Azure-ban.

A feladatokkal kapcsolatos forgatókönyvben úgy módosítja az alkalmazást, hogy befejezettként jelölhessen meg feladatokat.

### <a name="add-a-column"></a>Oszlop hozzáadása

Lépjen a Git-adattár gyökérkönyvtárába a terminálon.

Hozzon létre egy új migrálást, amely egy `Done` nevű logikai oszlopot ad hozzá a `Tasks` táblához:

```bash
rails generate migration AddDoneToTasks Done:boolean
```

Ez a parancs létrehoz egy új migrálási fájlt a _db/migrate_ könyvtárban.


A módosításnak a helyi adatbázisban való végrehajtásához futtassa a Rails adatbázis-migrálási parancsait a terminálon.

```bash
rake db:migrate
```

### <a name="update-application-logic"></a>Az alkalmazáslogika frissítése

Nyissa meg az *app/controllers/tasks_controller.rb* fájlt. Keresse meg a következő sort a fájl végén:

```rb
params.require(:task).permit(:Description)
```

Módosítsa a sort, hogy tartalmazza az új `Done` paramétert.

```rb
params.require(:task).permit(:Description, :Done)
```

### <a name="update-the-views"></a>A nézetek frissítése

Nyissa meg az *app/views/tasks/_form.html.erb* fájlt (Szerkesztési űrlap).

Keresse meg a `<%=f.error_span(:Description) %>` sort, majd szúrja be az alábbi kódot közvetlenül alá:

```erb
<%= f.label :Done, :class => 'control-label col-lg-2' %>
<div class="col-lg-10">
  <%= f.check_box :Done, :class => 'form-control' %>
</div>
```

Nyissa meg az *app/views/tasks/show.html.erb* fájlt (egyrekordos Nézet lap). 

Keresse meg a `<dd><%= @task.Description %></dd>` sort, majd szúrja be az alábbi kódot közvetlenül alá:

```erb
  <dt><strong><%= model_class.human_attribute_name(:Done) %>:</strong></dt>
  <dd><%= check_box "task", "Done", {:checked => @task.Done, :disabled => true}%></dd>
```

Nyissa meg az *app/views/tasks/index.html.erb* fájlt (az összes rekord Index lapja).

Keresse meg a `<th><%= model_class.human_attribute_name(:Description) %></th>` sort, majd szúrja be az alábbi kódot közvetlenül alá:

```erb
<th><%= model_class.human_attribute_name(:Done) %></th>
```

Ugyanabban a fájlban keresse meg a `<td><%= task.Description %></td>` sort, majd szúrja be az alábbi kódot közvetlenül alá:

```erb
<td><%= check_box "task", "Done", {:checked => task.Done, :disabled => true} %></td>
```

### <a name="test-the-changes-locally"></a>A módosítások helyi tesztelése

Futtassa a Rails-kiszolgálót a helyi terminálban.

```bash
rails server
```

A feladat állapotváltozásának megtekintéséhez lépjen a `http://localhost:3000` címre, és adjon hozzá vagy szerkesszen elemeket.

![A feladathoz hozzáadott jelölőnégyzet](./media/tutorial-ruby-postgres-app/complete-checkbox.png)

A Rails-kiszolgáló leállításához írja be a `Ctrl + C` billentyűparancsot a terminálon.

### <a name="publish-changes-to-azure"></a>Módosítások közzététele az Azure-ba

A módosítás Azure-adatbázisban való végrehajtásához futtasson Rails-adatbázis migrálásokat az éles környezetbe a terminálban.

```bash
rake db:migrate RAILS_ENV=production
```

Véglegesítse az összes módosítást a Gitben, majd továbbítsa a kód módosításait az Azure-ba.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

A `git push` befejezését követően navigáljon az Azure-alkalmazáshoz, és tesztelje az új funkciókat.

![Az Azure-ban közzétett modell- és adatbázis-módosítások](media/tutorial-ruby-postgres-app/complete-checkbox-published.png)

Ha felvett feladatokat, azok megmaradnak az adatbázisban. Az adatséma frissítései érintetlenül hagyják a meglévő adatokat.

## <a name="stream-diagnostic-logs"></a>Diagnosztikai naplók streamelése

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-the-azure-app"></a>Az Azure-alkalmazás kezelése

A létrehozott alkalmazás kezeléséhez lépjen a [Azure Portal](https://portal.azure.com) .

A bal oldali menüben kattintson a **app Services**elemre, majd kattintson az Azure-alkalmazás nevére.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/tutorial-php-mysql-app/access-portal.png)

Megtekintheti az alkalmazás áttekintés lapját. Itt elvégezhet olyan alapszintű felügyeleti feladatokat, mint a leállítás, elindítás, újraindítás, tallózás és törlés.

A bal oldali menü az alkalmazás konfigurálásához biztosít oldalakat.

![Az App Service lap az Azure Portalon](./media/tutorial-php-mysql-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Postgres-adatbázis létrehozása az Azure-ban
> * Ruby on Rails-alkalmazás csatlakoztatása a Postgreshez
> * Az alkalmazás üzembe helyezése az Azure-ban
> * Az adatmodell frissítése és az alkalmazás ismételt üzembe helyezése
> * Diagnosztikai naplók streamelése az Azure-ból
> * Az alkalmazás kezelése az Azure Portalon

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan képezhető le egyéni DNS-név az alkalmazáshoz.

> [!div class="nextstepaction"]
> [Oktatóanyag: egyéni DNS-név leképezése az alkalmazáshoz](../app-service-web-tutorial-custom-domain.md)

Vagy tekintse meg a többi erőforrást:

> [!div class="nextstepaction"]
> [Ruby-alkalmazás konfigurálása](configure-language-ruby.md)

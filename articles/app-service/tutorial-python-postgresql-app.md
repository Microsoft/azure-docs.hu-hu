---
title: 'Oktatóanyag: Python Django-alkalmazás üzembe helyezése a postgres-mel'
description: Hozzon létre egy PostgreSQL-adatbázist tartalmazó Python-webalkalmazást, és telepítse azt az Azure-ba. Az oktatóanyag a Django keretrendszert használja, és az alkalmazás Azure App Service Linux rendszeren található.
ms.devlang: python
ms.topic: tutorial
ms.date: 02/02/2021
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
- devx-track-python
- devx-track-azurecli
ms.openlocfilehash: a9f8fe10c5ffa787a6c170a29188cba21427b602
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175158"
---
# <a name="tutorial-deploy-a-django-web-app-with-postgresql-in-azure-app-service"></a>Oktatóanyag: Django-webalkalmazás üzembe helyezése a PostgreSQL-sel Azure App Service

Ebből az oktatóanyagból megtudhatja, hogyan helyezhet üzembe egy adatvezérelt Python [Django](https://www.djangoproject.com/) [Azure app Service](overview.md) -webalkalmazást, és hogyan csatlakoztatható egy Azure Database for postgres-adatbázishoz. A App Service egy jól méretezhető, önjavító webes üzemeltetési szolgáltatást nyújt.

Ebben az oktatóanyagban az Azure CLI használatával végezheti el a következő feladatokat:

> [!div class="checklist"]
> * A kezdeti környezet beállítása a Python és az Azure CLI használatával
> * Azure Database for PostgreSQL-adatbázis létrehozása
> * Kód üzembe helyezése Azure App Service és kapcsolódás a PostgreSQL-hez
> * A kód frissítése és újbóli üzembe helyezése
> * Diagnosztikai naplók megtekintése
> * A webalkalmazás kezelése a Azure Portalban

Használhatja az [oktatóanyag Azure Portal verzióját](/azure/developer/python/tutorial-python-postgresql-app-portal)is.


## <a name="1-set-up-your-initial-environment"></a>1. Állítsa be a kezdeti környezetét

1. Rendelkeznie kell aktív előfizetéssel rendelkező Azure-fiókkal. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
1. Telepítse a <a href="https://www.python.org/downloads/" target="_blank">Python 3,6-es vagy újabb</a>verzióját.
1. Telepítse az <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> -2.18.0 vagy újabb verzióját, amellyel az Azure-erőforrások kiépítéséhez és konfigurálásához bármilyen rendszerhéjban parancsokat futtathat.

Nyisson meg egy terminált, és győződjön meg arról, hogy a Python verziója 3,6 vagy újabb:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 --version
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```cmd
py -3 --version
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 --version
```

---

Győződjön meg arról, hogy az Azure CLI verziója 2.18.0 vagy magasabb:

```azurecli
az --version
```

Ha frissítenie kell, próbálja meg a `az upgrade` parancsot (a szükséges 2.11 + verziót igényel), vagy tekintse meg <a href="/cli/azure/install-azure-cli" target="_blank">Az Azure CLI telepítését</a>ismertető témakört.

Ezután jelentkezzen be az Azure-ba a CLI használatával:

```azurecli
az login
```

Ez a parancs egy böngészőt nyit meg a hitelesítő adatok összegyűjtéséhez. A parancs befejeződése után az előfizetésekkel kapcsolatos információkat tartalmazó JSON-kimenet jelenik meg.

Miután bejelentkezett, futtathatja az Azure-parancsokat az Azure CLI-vel, hogy az előfizetésében lévő erőforrásokkal működjön.

Problémák léptek fel? [Tudassa velünk](https://aka.ms/DjangoCLITutorialHelp).

## <a name="2-clone-or-download-the-sample-app"></a>2. a minta alkalmazás klónozása vagy letöltése

# <a name="git-clone"></a>[Git-klón](#tab/clone)

A minta tárház klónozása:

```terminal
git clone https://github.com/Azure-Samples/djangoapp
```

Ezután navigáljon a mappába:

```terminal
cd djangoapp
```

# <a name="download"></a>[Letöltés](#tab/download)

Keresse fel [https://github.com/Azure-Samples/djangoapp](https://github.com/Azure-Samples/djangoapp) a **klónozott** elemet, majd válassza a **zip letöltése** lehetőséget. 

Csomagolja ki a ZIP-fájlt egy *djangoapp* nevű mappába. 

Ezután nyisson meg egy terminál ablakot a *djangoapp* mappában.

---

A djangoapp minta tartalmazza az adatvezérelt Django-lekérdezési alkalmazást, amelyet az [első Django-alkalmazásnak](https://docs.djangoproject.com/en/3.1/intro/tutorial01/) a Django dokumentációjában történő megírásával talál. Az elkészült alkalmazást az Ön kényelme érdekében itt találja.

A minta úgy is módosul, hogy éles környezetben fusson, például App Service:

- Az üzemi beállítások a *azuresite/Production. file.* fájlba kerülnek. A fejlesztési beállítások a *azuresite/Settings.* a. a.
- Az alkalmazás éles beállításokat használ a `WEBSITE_HOSTNAME` környezeti változó beállításakor. A Azure App Service automatikusan beállítja ezt a változót a webalkalmazás URL-címére, például: `msdocs-django.azurewebsites.net` .

A termelési beállítások a Django konfigurálására szolgálnak az éles környezetben való futtatásra, és nem kifejezetten App Service. További információ: [Django Deployment ellenőrzőlista](https://docs.djangoproject.com/en/3.1/howto/deployment/checklist/). Tekintse meg az Azure-beli [Django üzemi beállításait](configure-language-python.md#production-settings-for-django-apps) is, ha néhány módosítással kapcsolatos információkat talál.

Problémák léptek fel? [Tudassa velünk](https://aka.ms/DjangoCLITutorialHelp).

## <a name="3-create-postgres-database-in-azure"></a>3. postgres-adatbázis létrehozása az Azure-ban

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](../postgresql/concepts-pricing-tiers.md#compute-generations-and-vcores) is available in your region. -->

Telepítse a `db-up` bővítményt az Azure CLI-hez:

```azurecli
az extension add --name db-up
```

Ha a `az` parancs nem ismerhető fel, győződjön meg arról, hogy az Azure CLI telepítve van [a kezdeti környezet beállítása](#1-set-up-your-initial-environment)című témakörben leírtak szerint.

Ezután hozza létre a postgres-adatbázist az Azure-ban a [`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) paranccsal:

```azurecli
az postgres up --resource-group DjangoPostgres-tutorial-rg --location westus2 --sku-name B_Gen5_1 --server-name <postgres-server-name> --database-name pollsdb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

- **Cserélje le** *\<postgres-server-name>* az **összes Azure** -ban egyedi névvel (a kiszolgálói végpont lesz `https://<postgres-server-name>.postgres.database.azure.com` ). A megfelelő minta a vállalat nevének és egy másik egyedi érték kombinációjának használata.
- És rendszer esetén a *\<admin-username>* *\<admin-password>* hitelesítő adatok megadásával hozzon létre egy rendszergazdai felhasználót ehhez a postgres-kiszolgálóhoz. A rendszergazda felhasználóneve nem lehet *azure_superuser*, *azure_pg_admin*, *Admin*, *Administrator*, *root*, *Guest* vagy *Public*. Nem kezdődhet *pg_*. A jelszónak **8 és 128 karakterből** kell állnia a következő kategóriák közül legalább háromból: angol nagybetűs karakterek, angol kisbetűs karakterek, számok (0 – 9) és nem alfanumerikus karakterek (például!, #,%). A jelszó nem tartalmazhatja a felhasználónevet.
- Ne használja a `$` karaktert a felhasználónévben vagy a jelszóban. Később olyan környezeti változókat hozhat létre ezekkel az értékekkel, amelyekben a `$` karakter speciális jelentéssel rendelkezik a Python-alkalmazások futtatásához használt Linux-tárolón belül.
- Az itt használt B_Gen5_1 (alapszintű, Gen5, 1 Core) [árképzési szint](../postgresql/concepts-pricing-tiers.md) a legkevésbé költséges. Éles adatbázisok esetében hagyja ki az `--sku-name` argumentumot, hogy ehelyett a GP_Gen5_2 (általános célú, Gen 5, 2 magok) szintet használja.

Ez a parancs a következő műveleteket hajtja végre, ami eltarthat néhány percig:

- Hozzon létre egy nevű [erőforráscsoportot](../azure-resource-manager/management/overview.md#terminology) `DjangoPostgres-tutorial-rg` , ha még nem létezik.
- Hozzon létre egy nevű postgres-kiszolgálót az `--server-name` argumentum alapján.
- Hozzon létre egy rendszergazdai fiókot a `--admin-user` és az `--admin-password` argumentumok használatával. Ezeket az argumentumokat kihagyva engedélyezheti, hogy a parancs egyedi hitelesítő adatokat állítson elő Önnek.
- Hozzon létre egy `pollsdb` adatbázist az argumentum nevével `--database-name` .
- Engedélyezze a hozzáférést a helyi IP-címről.
- Az Azure-szolgáltatásokból való hozzáférés engedélyezése.
- Hozzon létre egy adatbázis-felhasználót, amely hozzáfér az `pollsdb` adatbázishoz.

Az összes lépést külön is végrehajthatja más `az postgres` és más `psql` parancsokkal, de `az postgres up` az összes lépést együtt hajtja végre.

A parancs befejeződése után egy olyan JSON-objektumot ad vissza, amely különböző kapcsolódási karakterláncokat tartalmaz az adatbázishoz a kiszolgáló URL-címével együtt, egy generált felhasználónevet (például " joyfulKoala@msdocs-djangodb-12345 ") és egy GUID-jelszót. **Másolja a felhasználónevet és a jelszót egy ideiglenes szövegfájlba** , ahogy az oktatóanyag későbbi részében szüksége lesz rájuk.

<!-- not all locations support az postgres up -->
> [!TIP]
> `-l <location-name>`, az egyik [Azure-régióhoz](https://azure.microsoft.com/global-infrastructure/regions/)is beállítható. Az előfizetéshez elérhető régiókat a paranccsal érheti el [`az account list-locations`](/cli/azure/account#az-account-list-locations) . Éles alkalmazások esetében ugyanazon a helyen helyezze el az adatbázist és az alkalmazást.

Problémák léptek fel? [Tudassa velünk](https://aka.ms/DjangoCLITutorialHelp).

## <a name="4-deploy-the-code-to-azure-app-service"></a>4. a kód üzembe helyezése Azure App Service

Ebben a szakaszban az App Host alkalmazást App Service alkalmazásban hozza létre, kapcsolja össze az alkalmazást a postgres-adatbázissal, majd telepítse a kódot a gazdagépre.

### <a name="41-create-the-app-service-app"></a>4,1 a App Service alkalmazás létrehozása

Győződjön meg arról, hogy a terminálban a *djangoapp* adattár mappában található, amely tartalmazza az alkalmazás kódját.

Hozzon létre egy App Service alkalmazást (a gazdagép folyamatát) a következő [`az webapp up`](/cli/azure/webapp#az-webapp-up) paranccsal:

```azurecli
az webapp up --resource-group DjangoPostgres-tutorial-rg --location westus2 --plan DjangoPostgres-tutorial-plan --sku B1 --name <app-name>
```
<!-- without --sku creates PremiumV2 plan -->

- Az `--location` argumentum esetében ugyanazt a helyet használja, mint az előző szakaszban található adatbázishoz.
- **Cserélje le** *\<app-name>* az összes Azure-beli egyedi névvel (a kiszolgálói végpont `https://<app-name>.azurewebsites.net` ). A következőhöz engedélyezett karakterek:, *\<app-name>* `A` - `Z` `0` - `9` és `-` . Jó példa a vállalat nevének és az alkalmazás-azonosító kombinációjának használatára.

Ez a parancs a következő műveleteket hajtja végre, ami eltarthat néhány percig:

<!-- - Create the resource group if it doesn't exist. `--resource-group` is optional. -->
<!-- No it doesn't. az webapp up doesn't respect --resource-group -->
- Ha még nem létezik, hozza létre az [erőforráscsoportot](../azure-resource-manager/management/overview.md#terminology) . (Ebben a parancsban ugyanazt az erőforráscsoportot használja, amelyben korábban létrehozta az adatbázist.)
- Ha nem létezik, hozza létre [app Service](overview-hosting-plans.md) az alapszintű díjszabási csomag (B1) *DjangoPostgres* . `--plan` és `--sku` nem kötelező.
- Ha nem létezik, hozza létre a App Service alkalmazást.
- Az alkalmazás alapértelmezett naplózásának engedélyezése, ha még nincs engedélyezve.
- Töltse fel az adattárat a ZIP-telepítéssel a Build Automation használatával.
- Gyorsítótárazza az általános paramétereket, például az erőforráscsoport nevét és a App Service tervet a fájlba *. Azure/config*. Ennek eredményeképpen nem kell megadnia ugyanazt a paramétert a későbbi parancsokkal. Ha például a módosítások elvégzése után szeretné újból üzembe helyezni az alkalmazást, egyszerűen futtathatja `az webapp up` azokat paraméterek nélkül. A CLI-bővítményből származó parancsok, például a `az postgres up` (z) azonban nem a gyorsítótárban jelennek meg, ezért az erőforráscsoport és a hely megadására van szükség a kezdeti használatával `az webapp up` .

Sikeres telepítés esetén a parancs a következő példához hasonló JSON-kimenetet hoz létre:

![Példa az WebApp up Command output](./media/tutorial-python-postgresql-app/az-webapp-up-output.png)

Problémák léptek fel? Először olvassa el a [hibaelhárítási útmutatót](configure-language-python.md#troubleshooting), ha nem, [tudassa velünk](https://aka.ms/DjangoCLITutorialHelp).

### <a name="42-configure-environment-variables-to-connect-the-database"></a>4,2 környezeti változók konfigurálása az adatbázishoz való kapcsolódáshoz

Ha a kód már telepítve van a App Servicere, a következő lépés az alkalmazás összekötése a postgres-adatbázissal az Azure-ban.

Az alkalmazás kódja a következő négy környezeti változóban található adatbázis-információk megkeresésére vár:,, `DBHOST` `DBNAME` `DBUSER` és `DBPASS` .

A környezeti változók App Serviceban történő beállításához hozza létre az "Alkalmazásbeállítások" kifejezést a következő az [WebApp config appSettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) paranccsal.

```azurecli
az webapp config appsettings set --settings DBHOST="<postgres-server-name>" DBNAME="pollsdb" DBUSER="<username>" DBPASS="<password>"
```

- Cserélje le a *\<postgres-server-name>* nevet a paranccsal korábban használt névre `az postgres up` . A *azuresite/Production. a.* másolt kód automatikusan hozzáfűzi a `.postgres.database.azure.com` teljes postgres-kiszolgáló URL-címének létrehozásához.
- Cserélje *\<username>* le *\<password>* a és a kapcsolót a korábbi parancshoz használt rendszergazdai hitelesítő adatokkal `az postgres up` , illetve az `az postgres up` Ön által generált jogosultságokkal. A *azuresite/Production. a. a................* a kód automatikusan létrehozza a teljes postgres-felhasználónevet `DBUSER` a `DBHOST` `@server` (A korábban leírtaknak megfelelően nem használhatja a `$` karaktert sem, mivel a Linux környezeti változóinak speciális jelentése van.)
- Az erőforráscsoport és az alkalmazások nevei a *. Azure/config* fájl gyorsítótárazott értékeiből vannak kirajzolva.

A Python-kódban ezeket a beállításokat környezeti változókként, például a következő utasításokkal érheti el `os.environ.get('DBHOST')` . További információ: [hozzáférés környezeti változókhoz](configure-language-python.md#access-environment-variables).

Problémák léptek fel? Először olvassa el a [hibaelhárítási útmutatót](configure-language-python.md#troubleshooting), ha nem, [tudassa velünk](https://aka.ms/DjangoCLITutorialHelp).

### <a name="43-run-django-database-migrations"></a>4,3 Django adatbázis-áttelepítés futtatása

A Django-adatbázis áttelepítése biztosítja, hogy az Azure Database-ben található PostgreSQL-séma megfeleljen a kódban leírt sémának.

1. Nyisson meg egy SSH-munkamenetet **a böngészőben** , és lépjen a következő URL-címre, és jelentkezzen be az Azure-fiókja hitelesítő adataival (nem az adatbázis-kiszolgáló hitelesítő adataival).

    ```
    https://<app-name>.scm.azurewebsites.net/webssh/host
    ```

    Cserélje le a `<app-name>` nevet a parancsban korábban használt névre `az webapp up` .

    A paranccsal egy SSH-munkamenethez is csatlakozhat [`az webapp ssh`](/cli/azure/webapp#az_webapp_ssh) . Windows rendszeren ehhez a parancshoz az Azure CLI-2.18.0 vagy újabb verzió szükséges.

    Ha nem tud csatlakozni az SSH-munkamenethez, akkor maga az alkalmazás nem indult el. [A részletekért olvassa el a diagnosztikai naplókat](#6-stream-diagnostic-logs) . Ha például még nem hozta létre az alkalmazáshoz szükséges beállításokat az előző szakaszban, a naplók jelzik majd `KeyError: 'DBNAME'` .

1. Az SSH-munkamenetben futtassa a következő parancsokat (a **CTRL** + **SHIFT** V használatával is beilleszthet parancsokat + ):

    ```bash
    # Change to the app folder
    cd $APP_PATH
    
    # Activate the venv
    source /antenv/bin/activate

    # Install requirements
    pip install -r requirements.txt

    # Run database migrations
    python manage.py migrate

    # Create the super user (follow prompts)
    python manage.py createsuperuser
    ```

    Ha az adatbázishoz való csatlakozással kapcsolatos hibákba ütközik, tekintse meg az előző szakaszban létrehozott Alkalmazásbeállítások értékeit.

1. A `createsuperuser` parancs a rendszergazdai hitelesítő adatok megadását kéri. Ebben az oktatóanyagban használja az alapértelmezett felhasználónevet `root` , nyomja le az **ENTER** billentyűt az e-mail-címre, hogy üresen hagyja, és adja meg a `Pollsdb1` jelszót.

1. Ha hibaüzenet jelenik meg, hogy az adatbázis zárolva van, ellenőrizze, hogy az előző szakaszban futtatta-e a `az webapp settings` parancsot. Ezen beállítások nélkül az áttelepíthető parancs nem tud kommunikálni az adatbázissal, ami a hibát eredményezi.

Problémák léptek fel? Először olvassa el a [hibaelhárítási útmutatót](configure-language-python.md#troubleshooting), ha nem, [tudassa velünk](https://aka.ms/DjangoCLITutorialHelp).
    
### <a name="44-create-a-poll-question-in-the-app"></a>4,4 lekérdezési kérdés létrehozása az alkalmazásban

1. A böngészőben nyissa meg az URL-címet `http://<app-name>.azurewebsites.net` . Az alkalmazásnak meg kell jelennie a "lekérdezési alkalmazás" és a "nincsenek elérhető lekérdezések" üzenetnek, mert az adatbázisban még nincsenek adott lekérdezések.

    Ha az "alkalmazáshiba" üzenet jelenik meg, akkor valószínű, hogy az előző lépésben nem hozta létre a szükséges beállításokat, [konfigurálja a környezeti változókat az adatbázishoz való kapcsolódáshoz](#42-configure-environment-variables-to-connect-the-database), vagy ha ezek az értékek hibákat tartalmaznak. A `az webapp config appsettings list` beállítások megadásához futtassa a parancsot. [A diagnosztikai naplókban](#6-stream-diagnostic-logs) is megtekintheti az alkalmazások indításakor megadott hibákat. Ha például nem hozta létre a beállításokat, a naplók a következő hibaüzenetet fogják látni: `KeyError: 'DBNAME'` .

    Miután frissítette a beállításokat a hibák kijavítása érdekében, adjon egy percet az alkalmazásnak, majd frissítse a böngészőt.

1. Nyissa meg a következő címet: `http://<app-name>.azurewebsites.net/admin`. Jelentkezzen be a Django rendszergazdai hitelesítő adataival az előző szakaszban ( `root` és `Pollsdb1` ). A **lekérdezések** területen válassza a **Hozzáadás** a **kérdések** mellett lehetőséget, és hozzon létre egy lekérdezési kérdést néhány lehetőséggel.

1. Tallózással lépjen újra a gombra, `http://<app-name>.azurewebsites.net` és erősítse meg, hogy a kérdések most már jelen vannak a felhasználó számára. Válaszoljon a kérdésekre, azonban szeretne valamilyen adathalmazt előállítani az adatbázisban.

**Gratulálunk!** Egy Python Django-webalkalmazást futtat Azure App Service Linux rendszeren, aktív postgres-adatbázissal.

Problémák léptek fel? [Tudassa velünk](https://aka.ms/DjangoCLITutorialHelp).

> [!NOTE]
> App Service észleli a Django-projektet úgy, hogy minden almappában megkeres egy *WSGI.py* -fájlt, amely `manage.py startproject` alapértelmezés szerint létrehoz. Amikor App Service megkeresi a fájlt, betölti a Django-webalkalmazást. További információ: a [beépített Python-rendszerkép konfigurálása](configure-language-python.md).

## <a name="5-make-code-changes-and-redeploy"></a>5. kód módosításának és újbóli üzembe helyezésének tétele

Ebben a szakaszban helyi módosításokat hajt végre az alkalmazáson, és újból üzembe helyezi a kódot a App Service. A folyamat során beállíthat egy olyan Python virtuális környezetet, amely támogatja a folyamatos munkát.

### <a name="51-run-the-app-locally"></a>5,1 az alkalmazás helyi futtatása

Futtassa a következő parancsokat egy terminálablakban. Ügyeljen arra, hogy kövesse a következő utasításokat a rendszergazda létrehozásakor:

# <a name="bash"></a>[bash](#tab/bash)

```bash
# Configure the Python virtual environment
python3 -m venv venv
source venv/bin/activate

# Install dependencies
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
# Configure the Python virtual environment
py -3 -m venv venv
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned -Force
venv\scripts\activate

# Install dependencies
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```

# <a name="cmd"></a>[CMD](#tab/cmd)

```cmd
:: Configure the Python virtual environment
py -3 -m venv venv
venv\scripts\activate

:: Install dependencies
pip install -r requirements.txt
:: Run Django migrations
python manage.py migrate
:: Create Django superuser (follow prompts)
python manage.py createsuperuser
:: Run the dev server
python manage.py runserver
```
---

A webalkalmazás teljes betöltését követően a Django fejlesztői kiszolgáló a helyi alkalmazás URL-címét biztosítja a "fejlesztési kiszolgáló indítása a következő helyen" című üzenetben: http://127.0.0.1:8000/ . Lépjen ki a kiszolgálóról a CTRL-BREAK billentyűkombinációval.

![Példa a Django fejlesztői kiszolgáló kimenetére](./media/tutorial-python-postgresql-app/django-dev-server-output.png)

Tesztelje az alkalmazást helyileg a következő lépésekkel:

1. A böngészőben nyissa meg `http://localhost:8000` a "nincs elérhető lekérdezés" üzenetet. 

1. Nyissa meg a t, `http:///localhost:8000/admin` és jelentkezzen be a korábban létrehozott rendszergazda felhasználó használatával. A **lekérdezések** területen ismét válassza a **Hozzáadás** a **kérdések** mellett lehetőséget, és hozzon létre egy lekérdezési kérdést néhány lehetőséggel. 

1. Lépjen a *http: \/ /localhost: 8000* elemre, és válaszolja meg az alkalmazás tesztelésének kérdését. 

1. A **CTRL** C billentyűkombináció lenyomásával állítsa le a Django-kiszolgálót + .

Helyileg futtatva az alkalmazás egy helyi Sqlite3-adatbázist használ, és nem zavarja az éles adatbázisát. Igény szerint helyi PostgreSQL-adatbázist is használhat az éles környezet jobb szimulálása érdekében.

Problémák léptek fel? [Tudassa velünk](https://aka.ms/DjangoCLITutorialHelp).

### <a name="52-update-the-app"></a>5,2 az alkalmazás frissítése

A-ben `polls/models.py` Keresse meg a kezdetű sort, `choice_text` és módosítsa a `max_length` paramétert 100-re:

```python
# Find this lie of code and set max_length to 100 instead of 200
choice_text = models.CharField(max_length=100)
```

Mivel módosította az adatmodellt, hozzon létre egy új Django-áttelepítést, és telepítse át az adatbázist:

```
python manage.py makemigrations
python manage.py migrate
```

Futtassa újra a fejlesztői kiszolgálót, `python manage.py runserver` és tesztelje az alkalmazást a *http: \/ /localhost: 8000/admin* címen:

Állítsa le újra a Django webkiszolgálót a **CTRL C billentyűkombinációval** + .

Problémák léptek fel? Először olvassa el a [hibaelhárítási útmutatót](configure-language-python.md#troubleshooting), ha nem, [tudassa velünk](https://aka.ms/DjangoCLITutorialHelp).

### <a name="53-redeploy-the-code-to-azure"></a>5,3 a kód újbóli üzembe helyezése az Azure-ban

Futtassa a következő parancsot az adattár gyökerében:

```azurecli
az webapp up
```

Ez a parancs a *. Azure/config* fájlban gyorsítótárazott paramétereket használja. Mivel App Service észleli, hogy az alkalmazás már létezik, csak újra telepíti a kódot.

Problémák léptek fel? Először olvassa el a [hibaelhárítási útmutatót](configure-language-python.md#troubleshooting), ha nem, [tudassa velünk](https://aka.ms/DjangoCLITutorialHelp).

### <a name="54-rerun-migrations-in-azure"></a>5,4 Migrálás újrafuttatása az Azure-ban

Mivel módosította az adatmodellt, újra kell futtatnia az adatbázis-áttelepítést App Serviceban.

Nyisson meg egy SSH-munkamenetet a böngészőben, és navigáljon a következőre: `https://<app-name>.scm.azurewebsites.net/webssh/host` . Ezután futtassa le a következő parancsokat:

```
cd $APP_PATH
source /antenv/bin/activate
pip install -r requirements.txt
python manage.py migrate
```

Problémák léptek fel? Először olvassa el a [hibaelhárítási útmutatót](configure-language-python.md#troubleshooting), ha nem, [tudassa velünk](https://aka.ms/DjangoCLITutorialHelp).

### <a name="55-review-app-in-production"></a>5,5 felülvizsgálati alkalmazás éles környezetben

Keresse meg `http://<app-name>.azurewebsites.net` és tesztelje újra az alkalmazást éles környezetben. (Mivel csak az adatbázis mező hosszát módosította, a módosítás csak akkor észlelhető, ha egy kérdés létrehozásakor megpróbál hosszabb választ adni.)

Problémák léptek fel? Először olvassa el a [hibaelhárítási útmutatót](configure-language-python.md#troubleshooting), ha nem, [tudassa velünk](https://aka.ms/DjangoCLITutorialHelp).

## <a name="6-stream-diagnostic-logs"></a>6. stream diagnosztikai naplók

Elérheti az Azure-beli alkalmazást futtató tárolón belül létrehozott konzol naplóit.

Az alábbi Azure CLI-parancs futtatásával tekintheti meg a napló streamjét. Ez a parancs a *. Azure/config* fájlban gyorsítótárazott paramétereket használja.

```azurecli
az webapp log tail
```

Ha nem jelennek meg azonnal a konzolnaplófájlok, ellenőrizze ismét 30 másodperc múlva.

Ha bármikor le szeretné állítani a naplózási adatfolyamot, írja be a **CTRL C billentyűt** + .

Problémák léptek fel? [Tudassa velünk](https://aka.ms/DjangoCLITutorialHelp).

> [!NOTE]
> A naplófájlokat a böngészőből is megtekintheti a következő címen: `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.
>
> `az webapp up` az alapértelmezett naplózás bekapcsolása. A teljesítmény szempontjából ez a naplózás egy kis idő elteltével kikapcsolja magát, de minden alkalommal újra visszavált `az webapp up` . A manuális bekapcsolásához futtassa a következő parancsot:
>
> ```azurecli
> az webapp log config --docker-container-logging filesystem
> ```

## <a name="7-manage-your-app-in-the-azure-portal"></a>7. az alkalmazás kezelése a Azure Portalban

A [Azure Portal](https://portal.azure.com)keresse meg az alkalmazás nevét, és válassza ki az alkalmazást az eredmények között.

![Navigáljon a Python Django alkalmazáshoz a Azure Portal](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

Alapértelmezés szerint a portál az alkalmazás **Áttekintés** oldalát jeleníti meg, amely általános teljesítményt nyújt. Itt olyan alapszintű felügyeleti feladatokat is elvégezhet, mint a Tallózás, Leállítás, újraindítás és törlés. Az oldal bal oldalán lévő lapok a különböző megnyitható konfigurációs oldalakat jelenítik meg.

![A Python Django-alkalmazás kezelése a Azure Portal áttekintés lapján](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

Problémák léptek fel? Először olvassa el a [hibaelhárítási útmutatót](configure-language-python.md#troubleshooting), ha nem, [tudassa velünk](https://aka.ms/DjangoCLITutorialHelp).

## <a name="8-clean-up-resources"></a>8. erőforrások törlése

Ha meg szeretné tartani az alkalmazást, vagy folytassa a további oktatóanyagokkal, ugorjon a [következő lépésekre](#next-steps). Ellenkező esetben a folyamatos költségek elkerülése érdekében törölheti az ehhez az oktatóanyaghoz tartozó erőforráscsoport-létrehozási csoportot:

```azurecli
az group delete --no-wait
```

A parancs az *. Azure/config* fájlban gyorsítótárazott erőforráscsoport-nevet használja. Az erőforráscsoport törlésével felszabadítja és törli az abban található összes erőforrást is.

Az összes erőforrás törlése hosszabb időt is igénybe vehet. Az `--no-wait` argumentum lehetővé teszi, hogy a parancs azonnal visszalépjen.

Problémák léptek fel? [Tudassa velünk](https://aka.ms/DjangoCLITutorialHelp).

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan képezhető le egyéni DNS-név az alkalmazáshoz:

> [!div class="nextstepaction"]
> [Oktatóanyag: egyéni DNS-név leképezése az alkalmazáshoz](app-service-web-tutorial-custom-domain.md)

Ismerje meg, hogyan futtatja App Service egy Python-alkalmazást:

> [!div class="nextstepaction"]
> [Python-alkalmazás konfigurálása](configure-language-python.md)

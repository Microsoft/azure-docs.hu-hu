---
title: 'Oktatóanyag: Python Django-alkalmazás üzembe helyezése a Postgres használatával'
description: Python-webalkalmazás létrehozása PostgreSQL-adatbázissal, majd üzembe helyezése az Azure-ban. Az oktatóanyag a Django keretrendszert használja, és az alkalmazás a Azure App Service on Linux.
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
ms.openlocfilehash: 882a9fb0f8d528ca21cdc8149c60b9d5bdaf1723
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767094"
---
# <a name="tutorial-deploy-a-django-web-app-with-postgresql-in-azure-app-service"></a>Oktatóanyag: Django-webalkalmazás üzembe helyezése a PostgreSQL-lel a Azure App Service

Ez az oktatóanyag bemutatja, hogyan helyezhet üzembe [](overview.md) egy adatvezérelt Python [Django-webalkalmazást](https://www.djangoproject.com/) a Azure App Service és csatlakoztathatja egy Azure Database for Postgres-adatbázishoz. App Service egy nagymértékben skálázható, önjavítást lehetővé telő webes üzemeltetési szolgáltatást nyújt.

Ebben az oktatóanyagban az Azure CLI használatával a következő feladatokat használhatja:

> [!div class="checklist"]
> * Kezdeti környezet beállítása a Python és az Azure CLI használatával
> * Adatbázis Azure Database for PostgreSQL létrehozása
> * Kód üzembe helyezése Azure App Service PostgreSQL-hez való csatlakozáshoz
> * A kód frissítése és ismételt üzembe állás
> * Diagnosztikai naplók megtekintése
> * A webalkalmazás kezelése a Azure Portal

Használhatja az oktatóanyag [Azure Portal verzióját is.](/azure/developer/python/tutorial-python-postgresql-app-portal)


## <a name="1-set-up-your-initial-environment"></a>1. A kezdeti környezet beállítása

1. Aktív előfizetéssel rendelkezik egy Azure-fiókkal. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
1. Telepítse a <a href="https://www.python.org/downloads/" target="_blank">Python 3.6-os vagy újabb verzióját.</a>
1. Telepítse az <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> 2.18.0-s vagy újabb verzióját, amellyel parancsokat futtathat bármely rendszerhéjban az Azure-erőforrások kiépítése és konfigurálása érdekében.

Nyisson meg egy terminálablakot, és ellenőrizze, hogy a Python 3.6-os vagy újabb verziója van-e:

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

Ellenőrizze, hogy az Azure CLI 2.18.0-s vagy újabb verziója van-e:

```azurecli
az --version
```

Ha frissítenie kell, próbálja ki a parancsot `az upgrade` (ehhez a 2.11-es vagy újabb verzió szükséges), vagy tekintse meg <a href="/cli/azure/install-azure-cli" target="_blank">az Azure CLI telepítését.</a>

Ezután jelentkezzen be az Azure-ba a cli-ről:

```azurecli
az login
```

Ez a parancs megnyit egy böngészőt, amely összegyűjti a hitelesítő adatait. Amikor a parancs befejeződik, megjelenik az előfizetésekkel kapcsolatos információkat tartalmazó JSON-kimenet.

Miután bejelentkezett, Azure-parancsokat futtathat az Azure CLI-val az előfizetés erőforrásainak munkához.

Problémákat? [Tudajuk meg,](https://aka.ms/DjangoCLITutorialHelp)hogy .

## <a name="2-clone-or-download-the-sample-app"></a>2. A mintaalkalmazás klónozása vagy letöltése

# <a name="git-clone"></a>[Git-klónozás](#tab/clone)

Klónozza a mintaadattárat:

```terminal
git clone https://github.com/Azure-Samples/djangoapp
```

Ezután lépjen ebbe a mappába:

```terminal
cd djangoapp
```

# <a name="download"></a>[Letöltés](#tab/download)

Látogasson [https://github.com/Azure-Samples/djangoapp](https://github.com/Azure-Samples/djangoapp) el ide: , válassza **a Klónozás** lehetőséget, majd válassza **a ZIP letöltése lehetőséget.** 

Csomagolja ki a ZIP-fájlt egy *djangoapp nevű mappába.* 

Ezután nyisson meg egy terminálablakot a *djangoapp mappában.*

---

A djangoapp minta tartalmazza az adatvezérelt Django polls alkalmazást, amelyet az első [Django-alkalmazás](https://docs.djangoproject.com/en/3.1/intro/tutorial01/) írása a Django dokumentációjában talál. A kész alkalmazás az Ön kényelme érdekében itt található.

A minta úgy is módosul, hogy éles környezetben, például a következő App Service:

- Az éles beállítások az *azuresite/production.py fájlban* vannak. A fejlesztési beállítások az *azuresite/settings.py.*
- Az alkalmazás éles beállításokat használ a környezeti `WEBSITE_HOSTNAME` változó beállításakor. Azure App Service automatikusan a webalkalmazás URL-címére állítja be ezt a változót, `msdocs-django.azurewebsites.net` például: .

Az éles beállítások arra vonatkoznak, hogy a Django bármilyen éles környezetben fusson, és az éles környezetben való App Service. További információkért tekintse meg a [Django üzembe helyezési ellenőrzőlistát.](https://docs.djangoproject.com/en/3.1/howto/deployment/checklist/) A módosítások részleteiért tekintse meg az [Azure-beli Django](configure-language-python.md#production-settings-for-django-apps) éles beállításait is.

Problémákat? [Tudajuk meg,](https://aka.ms/DjangoCLITutorialHelp)hogy .

## <a name="3-create-postgres-database-in-azure"></a>3. Postgres-adatbázis létrehozása az Azure-ban

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](../postgresql/concepts-pricing-tiers.md#compute-generations-and-vcores) is available in your region. -->

Telepítse az `db-up` Azure CLI bővítményét:

```azurecli
az extension add --name db-up
```

Ha a parancs nem ismerhető fel, győződjön meg arról, hogy az Azure CLI telepítve van a `az` Kezdeti környezet beállítása szakaszban leírtak [szerint.](#1-set-up-your-initial-environment)

Ezután hozza létre a Postgres-adatbázist az Azure-ban az [`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) paranccsal:

```azurecli
az postgres up --resource-group DjangoPostgres-tutorial-rg --location westus2 --sku-name B_Gen5_1 --server-name <postgres-server-name> --database-name pollsdb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

- **Csere** *\<postgres-server-name>* az Azure-ban **egyedi** névvel (a kiszolgálóvégpont `https://<postgres-server-name>.postgres.database.azure.com` lesz). Jó minta lehet a vállalat nevének és egy másik egyedi értéknek a kombinációja.
- A és a számára adja meg a hitelesítő adatokat, hogy rendszergazdai felhasználót hozzon létre *\<admin-username>* *\<admin-password>* ehhez a Postgres-kiszolgálóhoz. A rendszergazdai felhasználónév nem lehet *azure_superuser* , *azure_pg_admin,* *rendszergazda,* *gyökér,* *vendég* vagy *nyilvános*. Nem kezdődhet a *pg_.* A jelszónak **8–128** karakter hosszúságúnak kell lennie a következő kategóriák közül legalább háromból: angol nagybetűs karakterek, angol kisbetűs karakterek, számok (0–9) és nem alfanumerikus karakterek (például !, #, %). A jelszó nem tartalmazhat felhasználónevet.
- Ne használja a `$` felhasználónévben vagy jelszóban szereplő karaktert. Később környezeti változókat fog létrehozni ezekkel az értékekkel, ahol a karakternek különleges jelentése van a Python-alkalmazások futtatásához használt `$` Linux-tárolón belül.
- Az B_Gen5_1 (Alapszintű, Gen5, 1 [magos)](../postgresql/concepts-pricing-tiers.md) tarifacsomag itt a legkevésbé költséges. Éles adatbázisok esetében a argumentum kihagyása a GP_Gen5_2 `--sku-name` (általános célú, Gen 5, 2 mag) szint használata helyett.

Ez a parancs a következő műveleteket végzi el, amelyek eltarthatnak néhány percig:

- Ha még [nem létezik,](../azure-resource-manager/management/overview.md#terminology) hozzon létre egy nevű `DjangoPostgres-tutorial-rg` erőforráscsoportot.
- Hozzon létre egy nevű Postgres-kiszolgálót a argumentum `--server-name` alapján.
- Hozzon létre egy rendszergazdai fiókot a és az `--admin-user` `--admin-password` argumentumok használatával. Kihagyhatja ezeket az argumentumokat, hogy a parancs egyedi hitelesítő adatokat hozzon létre.
- Hozzon `pollsdb` létre egy adatbázist a argumentum által `--database-name` elnevezett névvel.
- Engedélyezze a helyi IP-címről való hozzáférést.
- Azure-szolgáltatásokból való hozzáférés engedélyezése.
- Hozzon létre egy adatbázis-felhasználót, aki hozzáfér az `pollsdb` adatbázishoz.

Az összes lépést külön is végre lehet tenni más és `az postgres` `psql` parancsokkal, de az összes lépést együtt kell `az postgres up` elvégeznie.

Amikor a parancs befejeződik, egy JSON-objektumot ad vissza, amely az adatbázis különböző kapcsolati sztringjét tartalmazza a kiszolgáló URL-címével, a létrehozott felhasználónévvel (például " "), és joyfulKoala@msdocs-djangodb-12345 egy GUID-jelszóval. **Másolja a felhasználónevet és a jelszót** egy ideiglenes szövegfájlba, mert később szüksége lesz rájuk az oktatóanyagban.

<!-- not all locations support az postgres up -->
> [!TIP]
> `-l <location-name>`A bármelyik Azure-régióra [beállítható.](https://azure.microsoft.com/global-infrastructure/regions/) Az előfizetése számára elérhető régiókat az paranccsal [`az account list-locations`](/cli/azure/account#az_account_list_locations) kaphatja meg. Éles alkalmazások esetén helyezze az adatbázist és az alkalmazást ugyanoda.

Problémákat? [Tudajuk meg,](https://aka.ms/DjangoCLITutorialHelp)hogy .

## <a name="4-deploy-the-code-to-azure-app-service"></a>4. A kód üzembe helyezése a Azure App Service

Ebben a szakaszban alkalmazásgazdát hoz létre egy App Service alkalmazásban, csatlakoztatja ezt az alkalmazást a Postgres-adatbázishoz, majd üzembe helyezheti a kódot ezen a gazdagépen.

### <a name="41-create-the-app-service-app"></a>4.1 A App Service létrehozása

A terminálban győződjön meg arról, hogy az alkalmazáskódot tartalmazó *djangoapp* adattár mappában van.

Hozzon létre App Service alkalmazást (a gazdafolyamatot) az [`az webapp up`](/cli/azure/webapp#az_webapp_up) paranccsal:

```azurecli
az webapp up --resource-group DjangoPostgres-tutorial-rg --location westus2 --plan DjangoPostgres-tutorial-plan --sku B1 --name <app-name>
```
<!-- without --sku creates PremiumV2 plan -->

- A argumentumhoz használja ugyanazt a helyet, mint az előző szakaszban az `--location` adatbázishoz.
- **Csere** *\<app-name>* egyedi névvel az összes Azure-ban (a kiszolgálóvégpont `https://<app-name>.azurewebsites.net` a ). A engedélyezett *\<app-name>* karakterei a `A` - `Z` , a és `0` - `9` `-` a . Jó minta lehet a vállalat nevének és egy alkalmazásazonosítónak a kombinációja.

Ez a parancs a következő műveleteket végzi el, amelyek eltarthatnak néhány percig:

<!-- - Create the resource group if it doesn't exist. `--resource-group` is optional. -->
<!-- No it doesn't. az webapp up doesn't respect --resource-group -->
- Ha még [nem](../azure-resource-manager/management/overview.md#terminology) létezik, hozza létre az erőforráscsoportot. (Ebben a parancsban ugyanazt az erőforráscsoportot használja, amelyben korábban létrehozta az adatbázist.)
- Ha még [App Service,](overview-hosting-plans.md) hozza létre a *DjangoPostgres-tutorial-plan* csomagját az Alapszintű tarifacsomagban (B1). `--plan` A `--sku` és a nem kötelező.
- Hozza létre App Service alkalmazást, ha az még nem létezik.
- Engedélyezze az alapértelmezett naplózást az alkalmazáshoz, ha még nincs engedélyezve.
- Töltse fel az adattárat ZIP-alapú üzembe helyezéssel, engedélyezett buildautomatizálással.
- Gyorsítótárazza a gyakori paramétereket, például az erőforráscsoport nevét és a App Service tervét az *.azure/config fájlba.* Ennek eredményeképpen nem kell megadnia ugyanazt a paramétert későbbi parancsokkal. Ha például a módosításokat követően újra üzembe kell használhatja az alkalmazást, egyszerűen újra futtathatja az alkalmazást `az webapp up` paraméterek nélkül. A CLI-bővítményekből (például ) származó parancsok azonban jelenleg nem használják a gyorsítótárat, ezért az erőforráscsoportot és a helyet itt a kezdeti használatával kellett `az postgres up` `az webapp up` megadni.

Sikeres üzembe helyezés esetén a parancs az alábbi példához hasonló JSON-kimenetet hoz létre:

![Példa az az webapp up parancskimenetre](./media/tutorial-python-postgresql-app/az-webapp-up-output.png)

Problémákat? Először tekintse meg [a hibaelhárítási útmutatót,](configure-language-python.md#troubleshooting)egyéb esetben [pedig tudhatja velünk a következőt:](https://aka.ms/DjangoCLITutorialHelp).

### <a name="42-configure-environment-variables-to-connect-the-database"></a>4.2 Környezeti változók konfigurálása az adatbázis csatlakoztatásához

Most, hogy a kódot üzembe App Service, a következő lépés az alkalmazás csatlakoztatása a Postgres-adatbázishoz az Azure-ban.

Az alkalmazáskód azt várja, hogy az adatbázis-információkat négy környezeti változóban találja `DBHOST` meg , , és `DBNAME` `DBUSER` `DBPASS` névvel.

A környezeti változók beállítását a App Service [az az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) paranccsal hozhatja létre az "alkalmazásbeállításokat".

```azurecli
az webapp config appsettings set --settings DBHOST="<postgres-server-name>" DBNAME="pollsdb" DBUSER="<username>" DBPASS="<password>"
```

- Cserélje *\<postgres-server-name>* le a helyére a korábban használt nevet a `az postgres up` paranccsal. Az *azuresite/production.py* fájlban található kód automatikusan hozzáfűzi a kódot `.postgres.database.azure.com` a teljes Postgres-kiszolgálói URL-cím létrehozásához.
- Cserélje le a és a adatokat a korábbi paranccsal használt rendszergazdai hitelesítő adatokra, vagy az Ön számára *\<username>* *\<password>* létrehozott hitelesítő `az postgres up` `az postgres up` adatokra. Az *azuresite/production.py* kód automatikusan felépíti a teljes Postgres-felhasználónevet a és a rendszerből, ezért ne foglalja bele `DBUSER` `DBHOST` a `@server` részt. (Ahogy korábban említettük, egyik értékben sem szabad használni a karaktert, mert az a Linux környezeti változók esetében `$` különleges jelentéssel bír.)
- Az erőforráscsoport és az alkalmazás neve az *.azure/config* fájl gyorsítótárazott értékeiből lesz kirajzolva.

A Python-kódban ezeket a beállításokat környezeti változókként, például a utasításokkal lehet `os.environ.get('DBHOST')` elérni. További információ: [Környezeti változók elérése.](configure-language-python.md#access-environment-variables)

Problémákat? Először tekintse meg [a hibaelhárítási útmutatót,](configure-language-python.md#troubleshooting)egyéb esetben [pedig tudhatja velünk a következőt:](https://aka.ms/DjangoCLITutorialHelp).

### <a name="43-run-django-database-migrations"></a>4.3 Django-adatbázis migrálásának futtatása

A Django-adatbázis-migrálások biztosítják, hogy az Azure-adatbázis PostgreSQL-sémája megegyezik a kódban leírt sémával.

1. Nyisson meg  egy SSH-munkamenetet a böngészőben úgy, hogy megnyitja a következő URL-címet, és bejelentkezik az Azure-fiók hitelesítő adataival (nem az adatbázis-kiszolgáló hitelesítő adataival).

    ```
    https://<app-name>.scm.azurewebsites.net/webssh/host
    ```

    Cserélje `<app-name>` le a helyére a parancsban korábban használt `az webapp up` nevet.

    Másik lehetőségként kapcsolódhat egy SSH-munkamenethez az [`az webapp ssh`](/cli/azure/webapp#az_webapp_ssh) paranccsal. Windows rendszeren ehhez a parancshoz az Azure CLI 2.18.0-s vagy újabb verzió szükséges.

    Ha nem tud csatlakozni az SSH-munkamenethez, akkor maga az alkalmazás nem tudott elindulni. [A részletekért tekintse meg a diagnosztikai](#6-stream-diagnostic-logs) naplókat. Ha például még nem hozta létre a szükséges alkalmazásbeállításokat az előző szakaszban, a naplók a következőt fogják jelezni: `KeyError: 'DBNAME'` .

1. Az SSH-munkamenetben futtassa a következő parancsokat (a parancsokat a Ctrl Shift V **billentyűkombinációval** +  + **illesztheti be):**

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

    Ha az adatbázishoz való csatlakozással kapcsolatos hibákba ütközik, ellenőrizze az előző szakaszban létrehozott alkalmazásbeállítások értékeit.

1. A parancs felügyelői hitelesítő adatokat `createsuperuser` kér. Ebben az oktatóanyagban használja az alapértelmezett felhasználónevet, nyomja le az Enter billentyűt az e-mail-címhez, hogy üresen hagyja az e-mail-címet, és adja meg `root` a  `Pollsdb1` jelszót.

1. Ha azt a hibaüzenetet látja, hogy az adatbázis zárolva van, győződjön meg arról, hogy az előző szakaszban `az webapp settings` futtatta a parancsot. Ezen beállítások nélkül az áttelepítési parancs nem tud kommunikálni az adatbázissal, ami hibát eredményez.

Problémákat? Először tekintse meg [a hibaelhárítási útmutatót,](configure-language-python.md#troubleshooting)egyéb esetben [pedig tudhatja a következőt:](https://aka.ms/DjangoCLITutorialHelp).
    
### <a name="44-create-a-poll-question-in-the-app"></a>4.4 Lekérdezési kérdés létrehozása az alkalmazásban

1. Egy böngészőben nyissa meg a `http://<app-name>.azurewebsites.net` URL-címet. Az alkalmazásnak a "Polls app" (Lekérdezések alkalmazása) és a "No polls are available" (Nem érhető el lekérdezés) üzenetet kell megjelenítenie, mivel az adatbázisban még nincsenek meghatározott lekérdezések.

    Ha "Alkalmazáshiba" jelenik meg, akkor valószínű, hogy nem ön hozza létre a szükséges beállításokat az előző [lépésben,](#42-configure-environment-variables-to-connect-the-database)a Környezeti változók konfigurálása az adatbázis csatlakoztatásához, vagy hogy ezek az értékek hibákat tartalmaznak. Futtassa az `az webapp config appsettings list` parancsot a beállítások ellenőrzéséhez. A diagnosztikai [naplókban](#6-stream-diagnostic-logs) konkrét hibákat is láthat az alkalmazás indításakor. Ha például nem ön hozza létre a beállításokat, a naplókban a következő hibaüzenet jelenik meg: `KeyError: 'DBNAME'` .

    Miután frissítette a beállításokat a hibák kijavítása érdekében, adjon egy percet az alkalmazásnak az újraindításra, majd frissítse a böngészőt.

1. Nyissa meg a következő címet: `http://<app-name>.azurewebsites.net/admin`. Jelentkezzen be az előző szakaszban (és ) használt Django superuser hitelesítő `root` `Pollsdb1` adatokkal. A **Lekérdezések alatt** válassza a **Hozzáadás** lehetőséget a **Kérdések mellett,** és hozzon létre egy lekérdezési kérdést néhány választási lehetőség mellett.

1. Nyissa meg újra a böngészőt, és ellenőrizze, hogy a kérdések most `http://<app-name>.azurewebsites.net` már jelennek-e meg a felhasználó számára. Válaszoljon a kérdésekre, de szeretne adatokat létrehozni az adatbázisban.

**Gratulálunk!** Egy Python Django-webalkalmazást futtat linuxos Azure App Service- és aktív Postgres-adatbázissal.

Problémákat? [Tudajuk meg,](https://aka.ms/DjangoCLITutorialHelp)hogy .

> [!NOTE]
> App Service a Django-projektet úgy észleli,  hogy minden almappában egy wsgi.py fájlt keres, amely alapértelmezés `manage.py startproject` szerint létrejön. Amikor App Service a fájlt, betölti a Django-webalkalmazást. További információ: [Beépített Python-rendszerkép konfigurálása.](configure-language-python.md)

## <a name="5-make-code-changes-and-redeploy"></a>5. Kódváltozások és ismételt üzembe állás

Ebben a szakaszban helyi módosításokat fog tenni az alkalmazáson, majd újra üzembe App Service. A folyamat során egy pythonos virtuális környezetet fog beállítani, amely támogatja a folyamatos munkát.

### <a name="51-run-the-app-locally"></a>5.1 Az alkalmazás helyi futtatása

Futtassa a következő parancsokat egy terminálablakban. Mindenképpen kövesse az utasításokat a felügyelő létrehozásakor:

# <a name="bash"></a>[Bash](#tab/bash)

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

# <a name="cmd"></a>[Cmd](#tab/cmd)

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

A webalkalmazás teljes betöltése után a Django fejlesztői kiszolgáló a következő üzenetben tartalmazza a helyi alkalmazás URL-címét: "Starting development server at http://127.0.0.1:8000/ . Lépjen ki a kiszolgálóról a CTRL-BREAK billentyűkombinációval."

![Példa Django fejlesztői kiszolgáló kimenetére](./media/tutorial-python-postgresql-app/django-dev-server-output.png)

Az alkalmazás helyi teszteléséhez kövesse az alábbi lépéseket:

1. A böngészőben a következő üzenetet kell megjelenítenie: "Nincs elérhető `http://localhost:8000` lekérdezés". 

1. A oldalon jelentkezzen be a korábban létrehozott `http:///localhost:8000/admin` rendszergazdai felhasználóval. A **Lekérdezések alatt** válassza ismét a **Hozzáadás** lehetőséget a **Kérdések mellett,** és hozzon létre egy lekérdezési kérdést néhány választási lehetőség mellett. 

1. Látogasson el *ismét a \/ http: /localhost:8000* címre, és válaszoljon a kérdésre az alkalmazás teszteléséhez. 

1. Állítsa le a Django-kiszolgálót a Ctrl C **billentyű** + **lenyomásával.**

Helyi futtatáskor az alkalmazás egy helyi Sqlite3-adatbázist használ, és nem akadályozza az éles adatbázist. Szükség esetén helyi PostgreSQL-adatbázist is használhat az éles környezet jobb szimulálására.

Problémákat? [Tudajuk meg,](https://aka.ms/DjangoCLITutorialHelp)hogy .

### <a name="52-update-the-app"></a>5.2 Az alkalmazás frissítése

A -ben keresse meg az azzal kezdődik sort, és módosítsa a paramétert `polls/models.py` `choice_text` `max_length` 100-ra:

```python
# Find this line of code and set max_length to 100 instead of 200
choice_text = models.CharField(max_length=100)
```

Mivel módosította az adatmodellt, hozzon létre egy új Django-migrálást, és migrálja az adatbázist:

```
python manage.py makemigrations
python manage.py migrate
```

Futtassa újra a fejlesztői kiszolgálót a használatával, és `python manage.py runserver` tesztelje az alkalmazást a *következő címen: \/ /localhost:8000/admin:*

Állítsa le újra a Django-webkiszolgálót **a Ctrl** C + **billentyűkombinációval.**

Problémákat? Először tekintse meg [a hibaelhárítási útmutatót,](configure-language-python.md#troubleshooting)egyéb esetben [pedig tudhatja a következőt:](https://aka.ms/DjangoCLITutorialHelp).

### <a name="53-redeploy-the-code-to-azure"></a>5.3 A kód ismételt üzembe állása az Azure-ban

Futtassa a következő parancsot az adattár gyökérkönyvtárában:

```azurecli
az webapp up
```

Ez a parancs az *.azure/config* fájlban gyorsítótárazott paramétereket használja. Mivel App Service észleli, hogy az alkalmazás már létezik, csak újra üzembe fogja ásni a kódot.

Problémákat? Először tekintse meg [a hibaelhárítási útmutatót,](configure-language-python.md#troubleshooting)egyéb esetben [pedig tudhatja velünk a következőt:](https://aka.ms/DjangoCLITutorialHelp).

### <a name="54-rerun-migrations-in-azure"></a>5.4 Migrálások újrafuttassa az Azure-ban

Mivel módosításokat végrehajtott az adatmodellen, újra kell futtassa az adatbázis-migrálásokat a App Service.

Nyisson meg újra egy SSH-munkamenetet a böngészőben a következő megnyitásával: `https://<app-name>.scm.azurewebsites.net/webssh/host` . Ezután futtassa le a következő parancsokat:

```
cd $APP_PATH
source /antenv/bin/activate
pip install -r requirements.txt
python manage.py migrate
```

Problémákat? Először tekintse meg [a hibaelhárítási útmutatót,](configure-language-python.md#troubleshooting)egyéb esetben [pedig tudhatja velünk a következőt:](https://aka.ms/DjangoCLITutorialHelp).

### <a name="55-review-app-in-production"></a>5.5 Alkalmazás áttekintése éles környezetben

Nyissa meg `http://<app-name>.azurewebsites.net` és tesztelje újra az alkalmazást éles környezetben. (Mivel csak az adatbázismező hosszát módosította, a módosítás csak akkor lesz észrevehető, ha hosszabb választ próbál megadni egy kérdés létrehozásakor.)

Problémákat? Először tekintse meg [a hibaelhárítási útmutatót,](configure-language-python.md#troubleshooting)egyéb esetben [pedig tudhatja velünk a következőt:](https://aka.ms/DjangoCLITutorialHelp).

## <a name="6-stream-diagnostic-logs"></a>6. Diagnosztikai naplók streamelése

A létrehozott konzolnaplókat az alkalmazást az Azure-ban tárolóban lehet elérni.

Futtassa a következő Azure CLI-parancsot a naplóstreamek futtatásához. Ez a parancs az *.azure/config* fájlban gyorsítótárazott paramétereket használja.

```azurecli
az webapp log tail
```

Ha nem jelennek meg azonnal a konzolnaplófájlok, ellenőrizze ismét 30 másodperc múlva.

A naplóstreamelést bármikor leállíthatja a **Ctrl** C + **billentyűkombinációval.**

Problémákat? [Tudajuk meg,](https://aka.ms/DjangoCLITutorialHelp)hogy .

> [!NOTE]
> A naplófájlokat a böngészőből is megtekintheti a következő címen: `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.
>
> `az webapp up` A bekapcsolja az alapértelmezett naplózást. Teljesítménybeli okokból ez a naplózás egy idő után kikapcsol, de minden alkalommal újra bekapcsol, amikor újra `az webapp up` fut. A manuális bekapcsoláshoz futtassa a következő parancsot:
>
> ```azurecli
> az webapp log config --docker-container-logging filesystem
> ```

## <a name="7-manage-your-app-in-the-azure-portal"></a>7. Az alkalmazás kezelése a Azure Portal

A [Azure Portal](https://portal.azure.com)keresse meg az alkalmazás nevét, és válassza ki az alkalmazást az eredmények közül.

![Lépjen a Python Django-alkalmazáshoz a Azure Portal](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

Alapértelmezés szerint a portálon megjelenik az alkalmazás **Áttekintés** lapja, amely általános teljesítménynézetet biztosít. Itt olyan alapvető felügyeleti feladatokat is végrehajthat, mint a tallózás, leállítás, újraindítás és törlés. Az oldal bal oldalán lévő lapok a különböző megnyitható konfigurációs oldalakat jelenítik meg.

![A Python Django-alkalmazás kezelése az áttekintési oldal Azure Portal](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

Problémákat? Először tekintse meg [a hibaelhárítási útmutatót,](configure-language-python.md#troubleshooting)egyéb esetben [pedig tudhatja a következőt:](https://aka.ms/DjangoCLITutorialHelp).

## <a name="8-clean-up-resources"></a>8. Erőforrások tisztítása

Ha meg szeretné tartani az alkalmazást, vagy folytatni szeretné a további oktatóanyagokkal, ugorjon a [Következő lépések lépésre.](#next-steps) Ellenkező esetben a folyamatban lévő díjak elkerülése érdekében törölheti az oktatóanyag erőforráscsoport-létrehozási erőforráscsoportját:

```azurecli
az group delete --no-wait
```

A parancs az *.azure/config fájlban gyorsítótárazott erőforráscsoport-nevet* használja. Az erőforráscsoport törlésével felszabadíthatja és törölheti az összes benne található erőforrást.

Az összes erőforrás törlése némi időt is igényel. A `--no-wait` argumentum lehetővé teszi, hogy a parancs azonnal visszaadja a parancsot.

Problémákat? [Tudajuk meg,](https://aka.ms/DjangoCLITutorialHelp)hogy .

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan lehet leképezni egy egyéni DNS-nevet az alkalmazásra:

> [!div class="nextstepaction"]
> [Oktatóanyag: Egyéni DNS-név leképezése az alkalmazásra](app-service-web-tutorial-custom-domain.md)

Megtudhatja, App Service futtat egy Python-alkalmazást:

> [!div class="nextstepaction"]
> [Python-alkalmazás konfigurálása](configure-language-python.md)

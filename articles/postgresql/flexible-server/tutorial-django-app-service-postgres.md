---
title: 'Oktatóanyag: Django-alkalmazás üzembe helyezése App Service és Azure Database for PostgreSQL – Rugalmas kiszolgáló (előzetes verzió) virtuális hálózaton'
description: Django-alkalmazás üzembe helyezése az App Serice-Azure Database for PostgreSQL – Rugalmas kiszolgáló (előzetes verzió) virtuális hálózaton
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 09/22/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 84fd0c368bcf39af1bae90dc83336ffdb8489768
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791516"
---
# <a name="tutorial-deploy-django-app-with-app-service-and-azure-database-for-postgresql---flexible-server-preview"></a>Oktatóanyag: Django-alkalmazás üzembe helyezése App Service és Azure Database for PostgreSQL – Rugalmas kiszolgáló (előzetes verzió)

> [!IMPORTANT]
> Azure Database for PostgreSQL – A rugalmas kiszolgáló előzetes verzióban érhető el

Ez az oktatóanyag bemutatja, hogyan helyezhet üzembe Egy Django-alkalmazást az Azure-ban a rugalmas App Services és Azure Database for PostgreSQL – rugalmas kiszolgáló használatával egy virtuális hálózaton.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

Ehhez a cikkhez az Azure CLI 2.0-s vagy újabb verziójára lesz szükség helyileg. A telepített verziók megtekintéséhez futtassa az `az --version` parancsot. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

Jelentkezzen be a fiókjába az [az login paranccsal.](/cli/azure/authenticate-azure-cli) Jegyezze fel a megfelelő előfizetésnév parancskimenetéből az **id** tulajdonságot.

```azurecli
az login
```

Ha több előfizetéssel rendelkezik válassza ki a megfelelő előfizetést, amelyre az erőforrást terhelni szeretné. Válassza ki a megadott előfizetés-azonosítót a fiókja alatt az [az account set](/cli/azure/account) paranccsal. Helyettesítse **be** az **előfizetése az login** output parancsában található előfizetés-azonosító tulajdonságot az előfizetés-azonosító helyőrzőjére.

```azurecli
az account set --subscription <subscription id>
```
## <a name="clone-or-download-the-sample-app"></a>A mintaalkalmazás klónozása vagy letöltése

# <a name="git-clone"></a>[Git-klón](#tab/clone)

Klónozza a mintaadattárat:

```terminal
git clone https://github.com/Azure-Samples/djangoapp
```

Ezután a következő mappába kell ásni:

```terminal
cd djangoapp
```

# <a name="download"></a>[Letöltés](#tab/download)

Keresse fel [https://github.com/Azure-Samples/djangoapp](https://github.com/Azure-Samples/djangoapp) a webhelyet, **válassza a Clone (Klónozás)** lehetőséget, majd válassza a Download ZIP **(ZIP letöltése) lehetőséget.**

Csomagolja ki a ZIP-fájlt egy *djangoapp nevű mappába.*

Ezután nyisson meg egy terminálablakot a *djangoapp mappában.*

---

A djangoapp minta tartalmazza az adatvezérelt Django polls alkalmazást, amelyet az első [Django-alkalmazás](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) írása a Django dokumentációjában talál. A kész alkalmazás az Ön kényelme érdekében itt található.

A minta úgy is módosul, hogy éles környezetben, például a következő App Service:

- Az éles beállítások az *azuresite/production.py fájlban* vannak. A fejlesztési részleteket az *azuresite/settings.py tartalmazza.*
- Az alkalmazás éles beállításokat használ, ha `DJANGO_ENV` a környezeti változó értéke "production". Ezt a környezeti változót az oktatóanyag későbbi, a PostgreSQL-adatbázis konfigurációjában használt többi változóval együtt hozza létre.

Ezek a módosítások a Django bármely éles környezetben való futtatására való konfigurálásával kapcsolatosak, és nem specifikusak a App Service. További információkért tekintse meg a [Django üzembe helyezési ellenőrzőlistát.](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/)

## <a name="create-a-postgresql-flexible-server-in-a-new-virtual-network"></a>Rugalmas PostgreSQL-kiszolgáló létrehozása új virtuális hálózaton

Hozzon létre egy privát, rugalmas kiszolgálót és egy adatbázist egy virtuális hálózaton (VNET) a következő paranccsal:
```azurecli
# Create Flexible server in a VNET

az postgres flexible-server create --resource-group myresourcegroup --location westus2

```

Ez a parancs a következő műveleteket végzi el, amelyek eltarthatnak néhány percig:

- Ha még nem létezik, hozza létre az erőforráscsoportot.
- Létrehoz egy kiszolgálónevet, ha nincs megtéve.
- Hozzon létre egy új virtuális hálózatot az új postgreSQL-kiszolgálóhoz. **Jegyezze fel a** kiszolgálóhoz létrehozott virtuális hálózat és alhálózat nevét, mivel a webalkalmazást ugyanerhez a virtuális hálózathoz kell hozzáadnia.
- Rendszergazdai felhasználónevet, jelszót hoz létre a kiszolgálóhoz, ha nincs megtéve. **Jegyezze fel** a következő lépésben használni kívánt felhasználónevet és jelszót.
- Hozzon létre egy ```postgres``` fejlesztési célokra használható adatbázist. A [ **psql futtatásával** csatlakozhat az adatbázishoz](quickstart-create-server-portal.md#connect-to-the-postgresql-database-using-psql) egy másik adatbázis létrehozásához.

> [!NOTE]
> Jegyezze fel a jelszót, amely akkor lesz létrehozva, ha nincs megtéve. Ha elfelejti a jelszót, az paranccsal alaphelyzetbe kell ``` az postgres flexible-server update``` állítania a jelszót


## <a name="deploy-the-code-to-azure-app-service"></a>A kód üzembe helyezése a Azure App Service

Ebben a szakaszban alkalmazásgazdát hoz létre egy App Service alkalmazásban, csatlakoztatja ezt az alkalmazást a Postgres-adatbázishoz, majd üzembe helyezheti a kódot ezen a gazdagépen.


### <a name="create-the-app-service-web-app-in-a-virtual-network"></a>A App Service webalkalmazás létrehozása virtuális hálózaton

A terminálban győződjön meg arról, hogy az alkalmazáskódot tartalmazó adattár gyökerében ( `djangoapp` ) van.

Hozzon létre App Service alkalmazást (a gazdafolyamatot) az [`az webapp up`](/cli/azure/webapp#az_webapp_up) paranccsal:

```azurecli

# Create a web app
az webapp up --resource-group myresourcegroup --location westus2 --plan DjangoPostgres-tutorial-plan --sku B1 --name <app-name>

# Enable VNET integration for web app.
# Replace <vnet-name> and <subnet-name> with the virtual network and subnet name that the flexible server is using.

az webapp vnet-integration add -g myresourcegroup -n  mywebapp --vnet <vnet-name> --subnet <subnet-name>

# Configure database information as environment variables
# Use the postgres server name , database name , username , password for the database created in the previous steps

az webapp config appsettings set --settings DJANGO_ENV="production" DBHOST="<postgres-server-name>.postgres.database.azure.com" DBNAME="postgres" DBUSER="<username>" DBPASS="<password>"
```
- A argumentumhoz használja ugyanazt a helyet, mint az előző szakaszban az `--location` adatbázishoz.
- Cserélje le a helyére a teljes Azure-beli egyedi nevet *\<app-name>* (a kiszolgálóvégpont `https://\<app-name>.azurewebsites.net` a ). A engedélyezett *\<app-name>* karakterei a `A` - `Z` , a és `0` - `9` `-` a . Jó minta lehet a vállalat nevének és egy alkalmazásazonosítónak a kombinációja.
- Ha még [App Service,](../../app-service/overview-hosting-plans.md) hozza létre a *DjangoPostgres-tutorial-plan* csomagját az Alapszintű tarifacsomagban (B1). `--plan` A `--sku` és a nem kötelező.
- Hozza létre App Service alkalmazást, ha az még nem létezik.
- Engedélyezze az alapértelmezett naplózást az alkalmazáshoz, ha még nincs engedélyezve.
- Töltse fel az adattárat ZIP-alapú üzembe helyezéssel, engedélyezett buildautomatizálással.
- **Az az webapp vnet-integration parancs** a webalkalmazást a postgres-kiszolgálóval azonos virtuális hálózatban hozzáadja.
- Az alkalmazás kódja arra számít, hogy adatbázis-információkat fog találni számos környezeti változóban. A környezeti változók App Service az [az webapp config appsettings set paranccsal](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) hozhatja létre az "alkalmazásbeállításokat".

> [!TIP]
> Számos Azure CLI-parancs gyorsítótárazza az általános paramétereket, például az erőforráscsoport nevét és a App Service tervét az *.azure/config fájlba.* Ennek eredményeképpen nem kell megadnia ugyanazt a paramétert későbbi parancsokkal. Ha például a módosításokat követően újra üzembe kell használhatja az alkalmazást, egyszerűen újra futtathatja az alkalmazást `az webapp up` paraméterek nélkül.

### <a name="run-django-database-migrations"></a>Django-adatbázis migrálásának futtatása

A Django-adatbázis migrálásával biztosíthatja, hogy az Azure-adatbázis PostgreSQL-sémája megegyezik a kódban leírt sémával.

1. Nyisson meg egy SSH-munkamenetet a böngészőben az *\<app-name> https:// .scm.azurewebsites.net/webssh/host* megnyitásával, és jelentkezzen be az Azure-fiók hitelesítő adataival (nem az adatbázis-kiszolgáló hitelesítő adataival).

1. Az SSH-munkamenetben futtassa a következő parancsokat (a **parancsokat** a Ctrl Shift V + **billentyűkombinációval** + **illesztheti be):**

    ```bash
    cd site/wwwroot

    # Activate default virtual environment in App Service container
    source /antenv/bin/activate
    # Install packages
    pip install -r requirements.txt
    # Run database migrations
    python manage.py migrate
    # Create the super user (follow prompts)
    python manage.py createsuperuser
    ```

1. A parancs felügyelői hitelesítő adatokat `createsuperuser` kér. Ebben az oktatóanyagban használja az alapértelmezett felhasználónevet, nyomja le az Enter billentyűt az e-mail-címhez, hogy üresen hagyja, és írja be `root` a  `postgres1` jelszót.

### <a name="create-a-poll-question-in-the-app"></a>Lekérdezési kérdés létrehozása az alkalmazásban

1. Egy böngészőben nyissa meg a *http: \/ / \<app-name> .azurewebsites.net.* Az alkalmazásnak a "No polls are available" (Nincsenek elérhető lekérdezések) üzenetnek kell jelen lennie, mert az adatbázisban még nincsenek meghatározott lekérdezések.

1. Keresse meg a *http: \/ / \<app-name> .azurewebsites.net/admin.* Jelentkezzen be superuser hitelesítő adatokkal az előző szakaszban ( `root` és `postgres1` ). A **Lekérdezések alatt** válassza a **Hozzáadás** lehetőséget a **Kérdések mellett,** és hozzon létre egy lekérdezési kérdést néhány választási lehetőség mellett.

1. Nyissa meg újra a *http: \/ / \<app-name> .azurewebsites.net/,* hogy a kérdések jelennek-e meg a felhasználó számára. Válaszoljon a kérdésekre, de szeretne adatokat létrehozni az adatbázisban.

**Gratulálunk!** Egy Python Django-webalkalmazást futtat linuxos Azure App Service- és aktív Postgres-adatbázissal.

> [!NOTE]
> App Service a Django-projektet úgy észleli,  hogy minden almappában egy wsgi.py fájlt keres, amely alapértelmezés `manage.py startproject` szerint létrejön. Amikor App Service a fájlt, betölti a Django-webalkalmazást. További információ: [Beépített Python-rendszerkép konfigurálása.](../../app-service/configure-language-python.md)

## <a name="make-code-changes-and-redeploy"></a>Kódváltozások és ismételt üzembe ásás

Ebben a szakaszban helyi módosításokat fog tenni az alkalmazáson, majd újra üzembe App Service. A folyamat során egy pythonos virtuális környezetet fog beállítani, amely támogatja a folyamatos munkát.

### <a name="run-the-app-locally"></a>Az alkalmazás futtatása helyben

Futtassa a következő parancsokat egy terminálablakban. Mindenképpen kövesse az utasításokat a felügyelő létrehozásakor:

```bash
# Configure the Python virtual environment
python3 -m venv venv
source venv/bin/activate

# Install packages
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```
A webalkalmazás teljes betöltése után a Django fejlesztői kiszolgáló a következő üzenetben tartalmazza a helyi alkalmazás URL-címét: "Starting development server at http://127.0.0.1:8000/ . Lépjen ki a kiszolgálóról a CTRL-BREAK billentyűkombinációval."

:::image type="content" source="./media/tutorial-django-app-service-postgres/django-dev-server-output.png" alt-text="Példa Django fejlesztői kiszolgáló kimenetére":::

Az alkalmazás helyi teszteléséhez kövesse az alábbi lépéseket:

1. Látogasson el a *http: \/ /localhost:8000* címre egy böngészőben, ahol a "No polls are available" (Nem érhető el lekérdezés) üzenetnek kell lennie.

1. Látogasson el *a http: \/ /localhost:8000/admin* címre, és jelentkezzen be a korábban létrehozott rendszergazdai felhasználóval. A **Lekérdezések alatt** válassza ismét a **Hozzáadás** lehetőséget a **Kérdések mellett,** és hozzon létre egy lekérdezési kérdést néhány választási lehetőség mellett.

1. Látogasson el *ismét a \/ http: /localhost:8000* címre, és válaszoljon a kérdésre az alkalmazás teszteléséhez.

1. Állítsa le a Django-kiszolgálót a Ctrl C **billentyű** + **lenyomásával.**

Helyi futtatáskor az alkalmazás egy helyi Sqlite3-adatbázist használ, és nem akadályozza az éles adatbázist. Szükség esetén helyi PostgreSQL-adatbázist is használhat az éles környezet jobb szimulálására.



### <a name="update-the-app"></a>Az alkalmazás frissítése

A -ben keresse meg az azzal kezdődik sort, és módosítsa a paramétert `polls/models.py` `choice_text` `max_length` 100-ra:

```python
# Find this lie of code and set max_length to 100 instead of 200
choice_text = models.CharField(max_length=100)
```

Mivel módosította az adatmodellt, hozzon létre egy új Django-migrálást, és migrálja az adatbázist:

```
python manage.py makemigrations
python manage.py migrate
```

Futtassa újra a fejlesztői kiszolgálót a használatával, és `python manage.py runserver` tesztelje az alkalmazást a *következő címen: \/ /localhost:8000/admin:*

Állítsa le újra a Django-webkiszolgálót **a Ctrl** C + **billentyűkombinációval.**


### <a name="redeploy-the-code-to-azure"></a>A kód ismételt üzembe állása az Azure-ban

Futtassa a következő parancsot az adattár gyökérkönyvtárában:

```azurecli
az webapp up
```

Ez a parancs az *.azure/config fájlban gyorsítótárazott paramétereket* használja. Mivel App Service észleli, hogy az alkalmazás már létezik, csak a kódot fogja újra üzembe.



### <a name="rerun-migrations-in-azure"></a>Migrálások újrafuttassa az Azure-ban

Mivel módosításokat végrehajtott az adatmodellen, újra kell futtassa az adatbázis-migrálásokat a App Service.

Nyisson meg újra egy SSH-munkamenetet a böngészőben a *\<app-name> .https://.scm.azurewebsites.net/webssh/host.* Ezután futtassa le a következő parancsokat:

```
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
```

### <a name="review-app-in-production"></a>Alkalmazás áttekintése éles környezetben

Nyissa meg *a http: \/ / \<app-name> .azurewebsites.net,* és tesztelje újra az alkalmazást éles környezetben. (Mivel csak egy adatbázismező hosszát módosította, a módosítás csak akkor lesz észrevehető, ha hosszabb választ próbál megadni egy kérdés létrehozásakor.)

> [!TIP]
> A [django-storage-okkal](https://django-storages.readthedocs.io/en/latest/backends/azure.html) statikus és & azure-tárolókban tárolhatja. Statikus fájlok Azure CDN gzippinghez használhatja a fájlokat.


## <a name="manage-your-app-in-the-azure-portal"></a>Az alkalmazás kezelése a Azure Portal

A [Azure Portal](https://portal.azure.com)keresse meg az alkalmazás nevét, és válassza ki az alkalmazást az eredmények közül.

:::image type="content" source="./media/tutorial-django-app-service-postgres/navigate-to-django-app-in-app-services-in-the-azure-portal.png" alt-text="Lépjen a Python Django-alkalmazáshoz a Azure Portal":::

Alapértelmezés szerint a portálon megjelenik az alkalmazás **Áttekintés** lapja, amely általános teljesítménynézetet biztosít. Itt olyan alapvető felügyeleti feladatokat is végrehajthat, mint a tallózás, leállítás, újraindítás és törlés. Az oldal bal oldalán lévő lapok a különböző megnyitható konfigurációs oldalakat jelenítik meg.

:::image type="content" source="./media/tutorial-django-app-service-postgres/manage-django-app-in-app-services-in-the-azure-portal.png" alt-text="A Python Django-alkalmazás kezelése az áttekintési oldal Azure Portal":::


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha meg szeretné tartani az alkalmazást, vagy folytatni szeretné a következő oktatóanyagot, ugorjon a [Következő lépések lépésre.](#next-steps) Ellenkező esetben a folyamatban lévő díjak elkerülése érdekében törölheti az oktatóanyaghoz létrehozott erőforráscsoportot:

```azurecli
az group delete -g myresourcegroup
```

A parancs az *.azure/config fájlban gyorsítótárazott erőforráscsoport-nevet* használja. Az erőforráscsoport törlésével felszabadítja és törli a benne lévő összes erőforrást.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan lehet leképezni egy egyéni DNS-nevet az alkalmazásra:

> [!div class="nextstepaction"]
> [Oktatóanyag: Egyéni DNS-név leképezése az alkalmazásra](../../app-service/app-service-web-tutorial-custom-domain.md)

Megtudhatja, App Service futtat egy Python-alkalmazást:

> [!div class="nextstepaction"]
> [Python-alkalmazás konfigurálása](../../app-service/configure-language-python.md)

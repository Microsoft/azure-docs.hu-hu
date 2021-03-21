---
title: 'Oktatóanyag: a Django alkalmazás üzembe helyezése App Service és Azure Database for PostgreSQL rugalmas kiszolgálóval (előzetes verzió) a Virtual Network szolgáltatásban'
description: Django-alkalmazás üzembe helyezése az App Service és a Azure Database for PostgreSQL rugalmas kiszolgálóval (előzetes verzió) a Virtual Network szolgáltatásban
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 09/22/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: bcc9ca0175e0e03c62c2ce2b91d8ec337756a3cc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92490101"
---
# <a name="tutorial-deploy-django-app-with-app-service-and-azure-database-for-postgresql---flexible-server-preview"></a>Oktatóanyag: a Django alkalmazás üzembe helyezése App Service és Azure Database for PostgreSQL rugalmas kiszolgálóval (előzetes verzió)

> [!IMPORTANT]
> Azure Database for PostgreSQL – a rugalmas kiszolgáló előzetes verzióban érhető el

Ebből az oktatóanyagból megtudhatja, hogyan helyezhet üzembe egy Django-alkalmazást az Azure-ban App Services és Azure Database for PostgreSQL rugalmas kiszolgáló használatával egy virtuális hálózaton.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

Ehhez a cikkhez az Azure CLI 2,0-es vagy újabb verzióját kell futtatnia helyileg. A telepített verziók megtekintéséhez futtassa az `az --version` parancsot. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

Az az [login](/cli/azure/authenticate-azure-cli) parancs használatával kell bejelentkeznie a fiókjába. Jegyezze fel a megfelelő előfizetésnév parancskimenetéből az **id** tulajdonságot.

```azurecli
az login
```

Ha több előfizetéssel rendelkezik válassza ki a megfelelő előfizetést, amelyre az erőforrást terhelni szeretné. Válassza ki a megadott előfizetés-azonosítót a fiókja alatt az [az account set](/cli/azure/account) paranccsal. **Az előfizetés-azonosító tulajdonságot** az előfizetés-azonosító helyőrzőbe írja be az előfizetéshez tartozó **bejelentkezési** kimenetből.

```azurecli
az account set --subscription <subscription id>
```
## <a name="clone-or-download-the-sample-app"></a>A minta alkalmazás klónozása vagy letöltése

# <a name="git-clone"></a>[Git-klón](#tab/clone)

A minta tárház klónozása:

```terminal
git clone https://github.com/Azure-Samples/djangoapp
```

Ezután nyissa meg a mappát:

```terminal
cd djangoapp
```

# <a name="download"></a>[Letöltés](#tab/download)

Keresse fel [https://github.com/Azure-Samples/djangoapp](https://github.com/Azure-Samples/djangoapp) a **klónozott** elemet, majd válassza a **zip letöltése** lehetőséget.

Csomagolja ki a ZIP-fájlt egy *djangoapp* nevű mappába.

Ezután nyisson meg egy terminál ablakot a *djangoapp* mappában.

---

A djangoapp minta tartalmazza az adatvezérelt Django-lekérdezési alkalmazást, amelyet az [első Django-alkalmazásnak](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) a Django dokumentációjában történő megírásával talál. Az elkészült alkalmazást az Ön kényelme érdekében itt találja.

A minta úgy is módosul, hogy éles környezetben fusson, például App Service:

- Az üzemi beállítások a *azuresite/Production. file.* fájlba kerülnek. A fejlesztés részletei a *azuresite/Settings.* a. a.
- Az alkalmazás éles beállításokat használ, ha a `DJANGO_ENV` környezeti változó "éles" értékre van állítva. Ezt a környezeti változót később az oktatóanyagban fogja létrehozni, a PostgreSQL-adatbázis konfigurációjában használt többivel együtt.

Ezek a módosítások a Django konfigurálására szolgálnak az éles környezetben való futtatáshoz, és nem kifejezetten App Service. További információ: [Django Deployment ellenőrzőlista](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/).

## <a name="create-a-postgresql-flexible-server-in-a-new-virtual-network"></a>PostgreSQL rugalmas kiszolgáló létrehozása új virtuális hálózaton

Hozzon létre egy rugalmas kiszolgálót és egy, a virtuális hálózaton (VNET) belüli adatbázist a következő parancs használatával:
```azurecli
# Create Flexible server in a VNET

az postgres flexible-server create --resource-group myresourcegroup --location westus2

```

Ez a parancs a következő műveleteket hajtja végre, ami eltarthat néhány percig:

- Ha még nem létezik, hozza létre az erőforráscsoportot.
- Ha nincs megadva, a kiszolgáló nevét hozza létre.
- Hozzon létre egy új virtuális hálózatot az új postgreSQL-kiszolgálóhoz. Jegyezze fel a kiszolgálóhoz létrehozott **virtuális hálózat nevét és alhálózatának nevét** , mivel a webalkalmazást ugyanahhoz a virtuális hálózathoz kell felvennie.
- Rendszergazdai felhasználónevet és jelszót hoz létre a kiszolgálóhoz, ha nincs megadva. Jegyezze fel a következő lépésben használandó **felhasználónevet és jelszót** .
- Hozzon létre egy adatbázist ```postgres``` , amely fejlesztésre használható. [A **psql** futtatásával csatlakozhat az adatbázishoz](quickstart-create-server-portal.md#connect-to-the-postgresql-database-using-psql) egy másik adatbázis létrehozásához.

> [!NOTE]
> Jegyezze fel a jelszót, amelyet a rendszer akkor hoz majd meg, ha nincs megadva. Ha elfelejti a jelszót, a parancs használatával kell alaphelyzetbe állítani a jelszót ``` az postgres flexible-server update```


## <a name="deploy-the-code-to-azure-app-service"></a>A kód üzembe helyezése Azure App Service

Ebben a szakaszban az App Host alkalmazást App Service alkalmazásban hozza létre, kapcsolja össze az alkalmazást a postgres-adatbázissal, majd telepítse a kódot a gazdagépre.


### <a name="create-the-app-service-web-app-in-a-virtual-network"></a>A App Service Webalkalmazás létrehozása virtuális hálózaton

Győződjön meg arról, hogy a terminálon a tárház gyökerében ( `djangoapp` ) található, amely tartalmazza az alkalmazás kódját.

Hozzon létre egy App Service alkalmazást (a gazdagép folyamatát) a következő [`az webapp up`](/cli/azure/webapp#az-webapp-up) paranccsal:

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
- Az `--location` argumentum esetében ugyanazt a helyet használja, mint az előző szakaszban található adatbázishoz.
- Cserélje le *\<app-name>* az értékét az összes Azure-beli egyedi névre (a kiszolgálói végpont `https://\<app-name>.azurewebsites.net` ). A következőhöz engedélyezett karakterek:, *\<app-name>* `A` - `Z` `0` - `9` és `-` . Jó példa a vállalat nevének és az alkalmazás-azonosító kombinációjának használatára.
- Ha nem létezik, hozza létre [app Service](../../app-service/overview-hosting-plans.md) az alapszintű díjszabási csomag (B1) *DjangoPostgres* . `--plan` és `--sku` nem kötelező.
- Ha nem létezik, hozza létre a App Service alkalmazást.
- Az alkalmazás alapértelmezett naplózásának engedélyezése, ha még nincs engedélyezve.
- Töltse fel az adattárat a ZIP-telepítéssel a Build Automation használatával.
- az **WebApp vnet-Integration** parancs hozzáadja a webalkalmazást a postgres-kiszolgálót tartalmazó virtuális hálózatban.
- Az alkalmazás kódja számos környezeti változóban várja az adatbázis-információk megkeresését. A környezeti változók App Serviceban történő beállításához az az [WebApp config appSettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) paranccsal hozhatja létre az "Alkalmazásbeállítások" értéket.

> [!TIP]
> Számos Azure CLI-parancs gyorsítótárazza az általános paramétereket, például az erőforráscsoport nevét és a App Service tervet a fájlba *. Azure/config*. Ennek eredményeképpen nem kell megadnia ugyanazt a paramétert a későbbi parancsokkal. Ha például a módosítások elvégzése után szeretné újból üzembe helyezni az alkalmazást, egyszerűen futtathatja `az webapp up` azokat paraméterek nélkül.

### <a name="run-django-database-migrations"></a>Django adatbázis-áttelepítések futtatása

A Django-adatbázis áttelepítése biztosítja, hogy az Azure Database-ben található PostgreSQL-séma megfeleljen a kódban leírt sémának.

1. Nyisson meg egy SSH-munkamenetet a böngészőben, és lépjen a *https:// \<app-name> . SCM.azurewebsites.net/webssh/Host* webhelyre, és jelentkezzen be az Azure-fiókja hitelesítő adataival (nem az adatbázis-kiszolgáló hitelesítő adataival).

1. Az SSH-munkamenetben futtassa a következő parancsokat (a **CTRL** + **SHIFT** V használatával is beilleszthet parancsokat + ):

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

1. A `createsuperuser` parancs a rendszergazdai hitelesítő adatok megadását kéri. Ebben az oktatóanyagban használja az alapértelmezett felhasználónevet `root` , nyomja le az **ENTER** billentyűt az e-mail-címre, hogy üresen hagyja, és adja meg a `postgres1` jelszót.

### <a name="create-a-poll-question-in-the-app"></a>Lekérdezési kérdés létrehozása az alkalmazásban

1. A böngészőben nyissa meg a *http: \/ / \<app-name> . azurewebsites.net* URL-címet. Az alkalmazásnak meg kell jelennie a "nincs elérhető lekérdezés" üzenetnek, mert az adatbázisban még nincsenek adott lekérdezések.

1. Keresse meg a *http: \/ / \<app-name> . azurewebsites.net/admin* parancsot. Jelentkezzen be a rendszergazdai hitelesítő adatokkal az előző szakaszban ( `root` és `postgres1` ). A **lekérdezések** területen válassza a **Hozzáadás** a **kérdések** mellett lehetőséget, és hozzon létre egy lekérdezési kérdést néhány lehetőséggel.

1. Tallózással keresse meg újra a *http: \/ / \<app-name> . azurewebsites.net/* , és győződjön meg arról, hogy a kérdések most már jelen vannak a felhasználó számára. Válaszoljon a kérdésekre, azonban szeretne valamilyen adathalmazt előállítani az adatbázisban.

**Gratulálunk!** Egy Python Django-webalkalmazást futtat Azure App Service Linux rendszeren, aktív postgres-adatbázissal.

> [!NOTE]
> App Service észleli a Django-projektet úgy, hogy minden almappában megkeres egy *WSGI.py* -fájlt, amely `manage.py startproject` alapértelmezés szerint létrehoz. Amikor App Service megkeresi a fájlt, betölti a Django-webalkalmazást. További információ: a [beépített Python-rendszerkép konfigurálása](../../app-service/configure-language-python.md).

## <a name="make-code-changes-and-redeploy"></a>Kód módosításának és újbóli üzembe helyezésének tétele

Ebben a szakaszban helyi módosításokat hajt végre az alkalmazáson, és újból üzembe helyezi a kódot a App Service. A folyamat során beállíthat egy olyan Python virtuális környezetet, amely támogatja a folyamatos munkát.

### <a name="run-the-app-locally"></a>Az alkalmazás futtatása helyben

Futtassa a következő parancsokat egy terminálablakban. Ügyeljen arra, hogy kövesse a következő utasításokat a rendszergazda létrehozásakor:

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
A webalkalmazás teljes betöltését követően a Django fejlesztői kiszolgáló a helyi alkalmazás URL-címét biztosítja a "fejlesztési kiszolgáló indítása a következő helyen" című üzenetben: http://127.0.0.1:8000/ . Lépjen ki a kiszolgálóról a CTRL-BREAK billentyűkombinációval.

:::image type="content" source="./media/tutorial-django-app-service-postgres/django-dev-server-output.png" alt-text="Példa a Django fejlesztői kiszolgáló kimenetére":::

Tesztelje az alkalmazást helyileg a következő lépésekkel:

1. Nyissa meg a *http: \/ /localhost: 8000* -et egy böngészőben, amely a "nincs elérhető lekérdezés" üzenetet jeleníti meg.

1. Lépjen a *http: \/ /localhost: 8000/admin* webpontra, és jelentkezzen be a korábban létrehozott rendszergazda felhasználó használatával. A **lekérdezések** területen ismét válassza a **Hozzáadás** a **kérdések** mellett lehetőséget, és hozzon létre egy lekérdezési kérdést néhány lehetőséggel.

1. Lépjen a *http: \/ /localhost: 8000* elemre, és válaszolja meg az alkalmazás tesztelésének kérdését.

1. A **CTRL** C billentyűkombináció lenyomásával állítsa le a Django-kiszolgálót + .

Helyileg futtatva az alkalmazás egy helyi Sqlite3-adatbázist használ, és nem zavarja az éles adatbázisát. Igény szerint helyi PostgreSQL-adatbázist is használhat az éles környezet jobb szimulálása érdekében.



### <a name="update-the-app"></a>Az alkalmazás frissítése

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


### <a name="redeploy-the-code-to-azure"></a>A kód újbóli üzembe helyezése az Azure-ban

Futtassa a következő parancsot az adattár gyökerében:

```azurecli
az webapp up
```

Ez a parancs a *. Azure/config* fájlban gyorsítótárazott paramétereket használja. Mivel App Service észleli, hogy az alkalmazás már létezik, csak újra telepíti a kódot.



### <a name="rerun-migrations-in-azure"></a>Migrálás újrafuttatása az Azure-ban

Mivel módosította az adatmodellt, újra kell futtatnia az adatbázis-áttelepítést App Serviceban.

Nyisson meg egy SSH-munkamenetet a böngészőben a *https:// \<app-name> . SCM.azurewebsites.net/webssh/Host*. Ezután futtassa le a következő parancsokat:

```
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
```

### <a name="review-app-in-production"></a>Alkalmazás áttekintése éles környezetben

Keresse meg a *http: \/ / \<app-name> . azurewebsites.net* , és tesztelje újra az alkalmazást éles környezetben. (Mivel csak az adatbázis mező hosszát módosította, a módosítás csak akkor észlelhető, ha egy kérdés létrehozásakor megpróbál hosszabb választ adni.)

> [!TIP]
> Az Azure Storage [-ban Django-tárolók](https://django-storages.readthedocs.io/en/latest/backends/azure.html) használatával tárolhatja a statikus & adathordozó-eszközöket. A statikus fájlok gzipping Azure CDN is használhatja.


## <a name="manage-your-app-in-the-azure-portal"></a>Az alkalmazás kezelése a Azure Portalban

A [Azure Portal](https://portal.azure.com)keresse meg az alkalmazás nevét, és válassza ki az alkalmazást az eredmények között.

:::image type="content" source="./media/tutorial-django-app-service-postgres/navigate-to-django-app-in-app-services-in-the-azure-portal.png" alt-text="Navigáljon a Python Django alkalmazáshoz a Azure Portal":::

Alapértelmezés szerint a portál az alkalmazás **Áttekintés** oldalát jeleníti meg, amely általános teljesítményt nyújt. Itt olyan alapszintű felügyeleti feladatokat is elvégezhet, mint a Tallózás, Leállítás, újraindítás és törlés. Az oldal bal oldalán lévő lapok a különböző megnyitható konfigurációs oldalakat jelenítik meg.

:::image type="content" source="./media/tutorial-django-app-service-postgres/manage-django-app-in-app-services-in-the-azure-portal.png" alt-text="A Python Django-alkalmazás kezelése a Azure Portal áttekintés lapján":::


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha meg szeretné tartani az alkalmazást, vagy folytassa a következő oktatóanyaggal, ugorjon a [következő lépésekre](#next-steps). Ellenkező esetben a folyamatos költségek elkerülése érdekében törölheti az ehhez az oktatóanyaghoz tartozó erőforráscsoport-létrehozási csoportot:

```azurecli
az group delete -g myresourcegroup
```

A parancs az *. Azure/config* fájlban gyorsítótárazott erőforráscsoport-nevet használja. Az erőforráscsoport törlésével felszabadítja és törli az abban található összes erőforrást is.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan képezhető le egyéni DNS-név az alkalmazáshoz:

> [!div class="nextstepaction"]
> [Oktatóanyag: egyéni DNS-név leképezése az alkalmazáshoz](../../app-service/app-service-web-tutorial-custom-domain.md)

Ismerje meg, hogyan futtatja App Service egy Python-alkalmazást:

> [!div class="nextstepaction"]
> [Python-alkalmazás konfigurálása](../../app-service/configure-language-python.md)

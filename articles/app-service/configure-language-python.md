---
title: Linux-alapú Python-alkalmazások konfigurálása
description: Megtudhatja, hogyan konfigurálhatja azt a Python-tárolót, amelyben a webalkalmazások futnak a Azure Portal és az Azure CLI használatával.
ms.topic: quickstart
ms.date: 03/16/2021
ms.reviewer: astay; kraigb
ms.custom: mvc, seodec18, devx-track-python, devx-track-azurecli
ms.openlocfilehash: 605d1e0f67ac959d2c7325e04e2fd10d9d2419be
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829493"
---
# <a name="configure-a-linux-python-app-for-azure-app-service"></a>Linux Python-alkalmazás konfigurálása Azure App Service

Ez a cikk azt [ismerteti Azure App Service](overview.md) hogyan futtathat Python-alkalmazásokat, hogyan miolhat át meglévő alkalmazásokat az Azure-ba, és szükség esetén hogyan szabhatja testre a App Service viselkedését. A Python-alkalmazásokat az összes szükséges [pip modullal együtt kell üzembe](https://pypi.org/project/pip/) helyezni.

A App Service üzembe helyezési motor automatikusan aktivál egy virtuális környezetet, és a git-adattár vagy egy zip-csomag üzembe helyezésekor `pip install -r requirements.txt` [fut.](deploy-zip.md) [](deploy-local-git.md)

Ez az útmutató alapvető fogalmakat és utasításokat tartalmaz az olyan Python-fejlesztők számára, akik beépített Linux-tárolót App Service. Ha még soha nem használta a Azure App Service, először kövesse a [Python](quickstart-python.md) rövid útmutatóját és a [Python PostgreSQL-lel való használatával kapcsolatos oktatóanyagot.](tutorial-python-postgresql-app.md)

A konfigurációhoz használhatja [a Azure Portal](https://portal.azure.com) vagy az Azure CLI-t:

- **Azure Portal** az alkalmazás Settings Configuration (Beállítások  >  **konfigurálása)** lapját a Configure [an App Service app in the Azure Portal](configure-common.md).

- **Azure CLI:** Két lehetőség közül választhat.

    - Futtatassa a parancsokat a [Azure Cloud Shell.](../cloud-shell/overview.md)
    - Futtassa helyileg a parancsokat az [Azure CLI](/cli/azure/install-azure-cli)legújabb verziójának telepítésével, majd jelentkezzen be az Azure-ba az az [login paranccsal.](/cli/azure/reference-index#az_login)
    
> [!NOTE]
> A Linux jelenleg a Python-alkalmazások futtatásának ajánlott App Service. További információ a Windows lehetőségről: [Python a windowsos változatban App Service.](/visualstudio/python/managing-python-on-azure-app-service)

## <a name="configure-python-version"></a>Python-verzió konfigurálása

- **Azure Portal:** használja a  Konfiguráció lap Általános  beállítások lapját a [Linux-tárolók](configure-common.md#configure-general-settings) általános beállításainak konfigurálásával kapcsolatos cikknek megfelelően.

- **Azure CLI:**

    -  A Python aktuális verziójának megjelenítése [az az webapp config show használatával:](/cli/azure/webapp/config#az_webapp_config_show)
    
        ```azurecli
        az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
        ```
        
        Cserélje `<resource-group-name>` le a és a `<app-name>` helyére a webalkalmazásnak megfelelő neveket.
    
    - A Python-verzió beállítása az [az webapp config set használatával](/cli/azure/webapp/config#az_webapp_config_set)
        
        ```azurecli
        az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "PYTHON|3.7"
        ```
    
    - Az az [webapp list-runtimes](/cli/azure/webapp#az_webapp_list_runtimes)Azure App Service támogatott összes Python-verzió megjelenítése:
    
        ```azurecli
        az webapp list-runtimes --linux | grep PYTHON
        ```
    
A Python nem támogatott verzióját a saját tároló-rendszerképének megépítése alapján futtathatja. További információ: [egyéni Docker-rendszerkép használata](tutorial-custom-container.md?pivots=container-linux).

<!-- <a> element here to preserve external links-->
<a name="access-environment-variables"></a>

## <a name="customize-build-automation"></a>Buildautomatizálás testreszabása

App Service Oryx nevű buildrendszer a következő lépéseket végzi el, amikor Git- vagy zip-csomagokkal telepíti az alkalmazást:

1. Ha a beállítás meg van adva, egyéni build előtti `PRE_BUILD_COMMAND` szkriptet futtathat. (A szkript képes más Python- és Node.js szkripteket, pip- és npm-parancsokat, valamint node-alapú eszközöket ( például yarn) és `yarn install` `yarn build` .)

1. Futtassa az `pip install -r requirements.txt` parancsot. A *requirements.txt* fájlnak jelen kell lennie a projekt gyökérmappában. Ellenkező esetben a buildfolyamat a következő hibát jelenti: "Nem található setup.py vagy requirements.txt; Nem fut a pip telepítése."

1. Ha *manage.py* található a tárház gyökerében (ami Django-alkalmazást jelez), futtassa a *collectstatic manage.py futtatását.* Ha azonban a `DISABLE_COLLECTSTATIC` beállítás , akkor a rendszer `true` kihagyja ezt a lépést.

1. Ha a beállítás meg van adva, egyéni build utáni `POST_BUILD_COMMAND` szkriptet futtathat. (A szkript itt is futtathat más Python- és Node.js, pip- és npm-parancsokat, valamint Node-alapú eszközöket.)

Alapértelmezés szerint a `PRE_BUILD_COMMAND` , `POST_BUILD_COMMAND` és beállítások `DISABLE_COLLECTSTATIC` üresek. 

- Ha le szeretné tiltani a collectstatic futtatását a Django-alkalmazások létrehozása közben, állítsa a beállítást `DISABLE_COLLECTSTATIC` true (igaz) értékre.

- Az elő buildelési parancsok futtatásához állítsa a beállítást úgy, hogy tartalmazza a parancsot, például a parancsot, vagy a projekt gyökeréhez viszonyított parancsfájl elérési `PRE_BUILD_COMMAND` `echo Pre-build command` útját, például a következőt: `scripts/prebuild.sh` . Minden parancsnak relatív elérési utakat kell használnia a projekt gyökérmappa felé.

- A buildelés utáni parancsok futtatásához állítsa a beállítást úgy, hogy tartalmazza a parancsot, például a parancsot, vagy egy, a projekt gyökeréhez viszonyított szkriptfájl elérési `POST_BUILD_COMMAND` `echo Post-build command` útját, például a következőt: `scripts/postbuild.sh` . Minden parancsnak relatív elérési utakat kell használnia a projekt gyökérmappa felé.

A buildautomatizálást testre szabó további beállításokért lásd: [Oryx-konfiguráció.](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md) 

A build- és telepítési naplók eléréséhez lásd: [Access deployment logs (Telepítési naplók elérése).](#access-deployment-logs)

A Python-alkalmazások Linuxon való App Service és buildjére vonatkozó további információkért lásd: Python-alkalmazások észlelése és összeállítása az [Oryx használatával.](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/python.md)

> [!NOTE]
> A és a beállítások megegyeznek a és a `PRE_BUILD_SCRIPT_PATH` `POST_BUILD_SCRIPT_PATH` `PRE_BUILD_COMMAND` `POST_BUILD_COMMAND` beállításokkal, és örökölt célokra támogatottak.
> 
> Ha a beállítás `SCM_DO_BUILD_DURING_DEPLOYMENT` vagy 1-et tartalmaz, az oryx buildet vált ki az `true` üzembe helyezés során. A beállítás igaz a git, az Azure CLI parancs és a `az webapp up` Visual Studio használata esetén.

> [!NOTE]
> Mindig használjon relatív elérési utakat az összes build előtti és utáni szkriptben, mert az Oryx által futtatott buildtároló eltér az alkalmazás futtatásához használt futásidejű tárolótól. Soha ne támaszkodjon az alkalmazásprojekt mappájának pontos elhelyezésére a tárolóban (például hogy a *site/wwwroot mappában található).*

## <a name="migrate-existing-applications-to-azure"></a>Meglévő alkalmazások áttelepítése az Azure-ba

A meglévő webalkalmazások a következőképpen újra üzembe lehet őket használhatja az Azure-ban:

1. **Forrástár:** A forráskódot egy megfelelő adattárban, például a GitHubon tarthatja fenn, így a folyamat későbbi része lehetővé teszi a folyamatos üzembe helyezés beállítását.
    1. A *requirements.txt* fájlnak az adattár gyökerében kell lennie, hogy App Service a szükséges csomagokat.    

1. **Adatbázis:** Ha az alkalmazás egy adatbázistól függ, a szükséges erőforrásokat is az Azure-ban kell kiépítenünk. Példaként [lásd: Oktatóanyag: Django-webalkalmazás üzembe helyezése PostgreSQL-lel –](tutorial-python-postgresql-app.md#3-create-postgres-database-in-azure) adatbázis létrehozása.

1. **App Service-erőforrások:** Hozzon létre egy erőforráscsoportot, App Service- és App Service webalkalmazást az alkalmazáshoz. Ezt a kód első üzembe helyezésének az Azure CLI parancsával egyszerűen meg lehet tenni, ahogy az `az webapp up` az Oktatóanyag: [Django-webalkalmazás](tutorial-python-postgresql-app.md#4-deploy-the-code-to-azure-app-service)üzembe helyezése PostgreSQL-lel – a kód üzembe helyezése szakaszban látható. Cserélje le az erőforráscsoport, a App Service Plan és a webalkalmazás nevét, hogy jobban megfelel az alkalmazásnak.

1. **Környezeti változók:** Ha az alkalmazásnak környezeti változókra van szüksége, hozzon létre [App Service alkalmazásbeállításokkal.](configure-common.md#configure-app-settings) Ezek App Service beállítások környezeti változókként jelennek meg a kódban az [Access environment variables (Környezeti változók elérése) oldalon leírtak szerint.](#access-app-settings-as-environment-variables)
    - Az adatbázis-kapcsolatokat például gyakran ilyen beállításokkal kezelik, ahogyan az [Oktatóanyag: Django-webalkalmazás](tutorial-python-postgresql-app.md#42-configure-environment-variables-to-connect-the-database)üzembe helyezése a PostgreSQL-lel – változók konfigurálása az adatbázis csatlakoztatásához.
    - A [tipikus Django-alkalmazásokra](#production-settings-for-django-apps) vonatkozó konkrét beállításokért lásd: Production settings for Django apps (A Django-alkalmazások éles beállításai).

1. **Alkalmazás indítása:** Tekintse [](#container-startup-process) át a tárolóindítási folyamat című szakaszt a cikk későbbi részében, hogy megértse, hogyan App Service az alkalmazás futtatására. App Service a Gunicorn webkiszolgálót használja, amelynek meg kell találnia az alkalmazásobjektumot vagy wsgi.py *mappát.* Szükség esetén testre szabhatja [az indítási parancsot.](#customize-startup-command)

1. **Folyamatos üzembe** helyezés: Állítsa be a folyamatos üzembe helyezést a Continuous [deployment to Azure App Service](deploy-continuous-deployment.md) if using Azure Pipelines or Kudu deployment (Folyamatos üzembe helyezés a Azure App Service-be) vagy az Deploy to App Service using GitHub Actions if using GitHub actions (Folyamatos üzembe helyezés a Azure App Service-be) vagy Deploy to App Service using [GitHub Actions](./deploy-continuous-deployment.md) if using GitHub actions (Üzembe helyezés a App Service-ben GitHub-műveletek használata esetén).

1. **Egyéni műveletek:** Ha műveleteket szeretne végrehajtani az alkalmazást App Service tárolóban, például a Django-adatbázis migrálását, SSH-kapcsolaton keresztül csatlakozhat a [tárolóhoz.](configure-linux-open-ssh-session.md) A Django-adatbázis migrálásának futtatására vonatkozó példáért [lásd: Oktatóanyag: Django-webalkalmazás](tutorial-python-postgresql-app.md#43-run-django-database-migrations)üzembe helyezése PostgreSQL-lel – adatbázis-migrálások futtatása.
    - Folyamatos üzembe helyezés használata esetén ezeket a műveleteket a build utáni parancsokkal hajthatja végre a [build automatizálásának testreszabása korábban leírtak szerint.](#customize-build-automation)

A lépések befejezése után képesnek kell lennie a módosítások véglegesítésére a forrástárházban, és ezeket a frissítéseket automatikusan telepíteni a App Service.

### <a name="production-settings-for-django-apps"></a>A Django-alkalmazások éles beállításai

Éles környezetben( például Azure App Service a Django-alkalmazásoknak követnie kell a Django üzembe helyezési ellenőrzőlistát [(djangoproject.com).](https://docs.djangoproject.com/en/3.1/howto/deployment/checklist/)

Az alábbi táblázat az Azure-ral kapcsolatos éles beállításokat ismerteti. Ezek a beállítások az alkalmazás setting.py *vannak* megadva.

| Django-beállítás | Utasítások az Azure-hoz |
| --- | --- |
| `SECRET_KEY` | Tárolja az értéket egy App Service az [Access app settings as environment variables (Alkalmazásbeállítások elérése környezeti változókként) leírásban leírtak szerint.](#access-app-settings-as-environment-variables) Az értéket titkos [ként](../key-vault/secrets/quick-create-python.md)is tárolhatja a Azure Key Vault. |
| `DEBUG` | Hozzon `DEBUG` létre egy beállítást a App Service 0 (false) értékkel, majd töltse be az értéket környezeti változóként. A fejlesztési környezetben hozzon létre egy környezeti `DEBUG` változót 1 (igaz) értékkel. |
| `ALLOWED_HOSTS` | Éles környezetben a Django megköveteli, hogy az alkalmazás URL-címe szerepeljen a `ALLOWED_HOSTS` settings.py.  Ezt az URL-címet futásidőben a következő kóddal lehet lekérni: `os.environ['WEBSITE_HOSTNAME']` . App Service a környezeti `WEBSITE_HOSTNAME` változót automatikusan az alkalmazás URL-címére állítja be. |
| `DATABASES` | Adja meg a App Service adatbázis-kapcsolat beállításait, és töltse be őket környezeti változókként a szótár [`DATABASES`](https://docs.djangoproject.com/en/3.1/ref/settings/#std:setting-DATABASES) feltöltéséhez. Az értékeket (különösen a felhasználónevet és a jelszót) titkos Azure Key Vault [is tárolhatja.](../key-vault/secrets/quick-create-python.md) |

## <a name="serve-static-files-for-django-apps"></a>Statikus fájlok kiszolgálása Django-alkalmazásokhoz

Ha a Django-webalkalmazás statikus előoldali fájlokat [](https://docs.djangoproject.com/en/3.1/howto/static-files/) tartalmaz, először kövesse a Statikus fájlok kezelése útmutatót a Django dokumentációjában.

A App Service a következő módosításokat kell követnie:

1. Fontolja meg környezeti változók (helyi fejlesztéshez) és alkalmazásbeállítások (a felhőben való üzembe helyezéskor) alkalmazását a Django és a változók `STATIC_URL` `STATIC_ROOT` dinamikus beállításában. Például:    

    ```python
    STATIC_URL = os.environ.get("DJANGO_STATIC_URL", "/static/")
    STATIC_ROOT = os.environ.get("DJANGO_STATIC_ROOT", "./static/")    
    ```

    `DJANGO_STATIC_URL` és szükség szerint módosítható a `DJANGO_STATIC_ROOT` helyi és a felhőalapú környezetekben. Ha például a statikus fájlok buildfolyamata egy nevű mappába kerül, akkor a beállításával elkerülheti `django-static` `DJANGO_STATIC_URL` az alapértelmezett `/django-static/` értékeket.

1. Ha olyan előépítési szkriptje van, amely statikus fájlokat hoz létre egy másik mappában, akkor a Django-változóba foglalja bele ezt a mappát, hogy a Django folyamata megtalálja `STATICFILES_DIRS` `collectstatic` őket. Ha például az előérmappában futtatja a parancsot, és a yarn létrehoz egy statikus fájlokat tartalmazó mappát, akkor a következőképpen foglalja bele ezt `yarn build` a `build/static` mappát:

    ```python
    FRONTEND_DIR = "path-to-frontend-folder" 
    STATICFILES_DIRS = [os.path.join(FRONTEND_DIR, 'build', 'static')]    
    ```

    Itt ( ) egy olyan elérési út felépítéséhez, ahol a yarnhoz hasonló `FRONTEND_DIR` buildeszköz fut. Ismét szükség szerint használhat környezeti változót és alkalmazásbeállítást.

1. Adja `whitenoise` hozzá a *requirements.txt* fájlhoz. [A whitenoise](http://whitenoise.evans.io/en/stable/) (whitenoise.evans.io) egy Python-csomag, amely megkönnyíti az éles Django-alkalmazások számára a saját statikus fájljainak kiszolgálását. A whitenoise kifejezetten a Django változó által megadott mappában található fájlokat `STATIC_ROOT` szolgálja ki.

1. A *settings.py* adja hozzá a whitenoise következő sorát:

    ```python
    STATICFILES_STORAGE = ('whitenoise.storage.CompressedManifestStaticFilesStorage')
    ```

1. Módosítsa a és a `MIDDLEWARE` `INSTALLED_APPS` listákat is, hogy tartalmazzák a Whitenoise (Fehér) listákat:

    ```python
    MIDDLEWARE = [                                                                   
        'django.middleware.security.SecurityMiddleware',
        # Add whitenoise middleware after the security middleware                             
        'whitenoise.middleware.WhiteNoiseMiddleware',
        # Other values follow
    ]

    INSTALLED_APPS = [
        "whitenoise.runserver_nostatic",
        # Other values follow
    ]
    ```

## <a name="container-characteristics"></a>A tároló jellemzői

A Python-App Service üzembe helyezésekor a Python-alkalmazások a Python [GitHub-adattárban](https://github.com/Azure-App-Service/python)meghatározott Linux Docker App Service tárolóban futnak. A rendszerkép-konfigurációkat a verzióspecifikus könyvtárakban találja.

Ez a tároló a következő jellemzőkkel rendelkezik:

- Az alkalmazások a [Gunicorn WSGI HTTP-kiszolgáló](https://gunicorn.org/) és további argumentumok használatával futnak`--bind=0.0.0.0 --timeout 600`.
    - A Gunicorn konfigurációs beállításait *a* projekt gyökerében található gunicorn.conf.py meg, a [Gunicorn](https://docs.gunicorn.org/en/stable/configure.html#configuration-file) konfigurációjának áttekintésében (docs.gunicorn.org). Alternatív megoldásként testre [is szabhatja az indítási parancsot.](#customize-startup-command)

    - A webalkalmazás véletlen vagy szándékos DDOS-támadások elleni védelme érdekében a Gunicorn egy Nginx fordított proxy mögött fut a [Gunicorn](https://docs.gunicorn.org/en/latest/deploy.html) üzembe helyezése (docs.gunicorn.org).

- Alapértelmezés szerint az alap tároló rendszerképe csak a Flask webes keretrendszert tartalmazza, de a tároló más, WSGI-kompatibilis és Python 3.6-os vagy azzal kompatibilis keretrendszereket is támogat, például a Django keretrendszert.

- További csomagok, például [*a*](https://pip.pypa.io/en/stable/user_guide/#requirements-files) Django telepítéséhez hozzon létre egyrequirements.txt-fájlt a projekt gyökerében, amely meghatározza a közvetlen függőségeket. App Service automatikusan telepíti ezeket a függőségeket a projekt üzembe helyezésekor.

    A *requirements.txt* *fájlnak a* projekt gyökerében kell lennie a függőségek telepítéséhez. Ellenkező esetben a buildfolyamat a következő hibát jelenti: "Nem található setup.py vagy requirements.txt; Nem fut a pip telepítése." Ha ezzel a hibával találkozik, ellenőrizze a követelmények fájl helyét.

- App Service automatikusan definiál egy nevű környezeti változót a `WEBSITE_HOSTNAME` webalkalmazás URL-címével, `msdocs-hello-world.azurewebsites.net` például: . Emellett az alkalmazás `WEBSITE_SITE_NAME` nevével is meghatározza, `msdocs-hello-world` például: . 
   
- Az npm Node.js és a tárolóba vannak telepítve, így Node-alapú buildeszközöket, például yarnokat futtathat.

## <a name="container-startup-process"></a>Tároló indítási folyamata

Rendszerindítás során a Linux-tárolóban lévő App Service a következő lépéseket futtatja:

1. Ha meg [van téve, használjon](#customize-startup-command)egyéni indítási parancsot.
2. Ellenőrizze, hogy létezik-e [Django-alkalmazás,](#django-app)és indítsa el hozzá a Gunicorn alkalmazást, ha azt észleli.
3. Ellenőrizze, hogy létezik-e [Flask-alkalmazás,](#flask-app)és ha észlelte, indítsa el a Gunicorn alkalmazást.
4. Ha más alkalmazás nem található, indítson el egy alapértelmezett alkalmazást, amely a tárolóba van beépítve.

A következő szakaszok további információkkal szolgálnak az egyes beállításokról.

### <a name="django-app"></a>Django-alkalmazás

Django-alkalmazások esetén az App Service a(z) `wsgi.py` nevű fájlt keresi az alkalmazáskódban, majd a Gunicorn futtatásához a következő parancsot használja:

```bash
# <module> is the name of the folder that contains wsgi.py
gunicorn --bind=0.0.0.0 --timeout 600 <module>.wsgi
```

Ha pontosabb vezérlést szeretne az indítási parancs felett, használjon egy egyéni indítási [parancsot,](#customize-startup-command)cserélje le a helyére a wsgi.py-t tartalmazó mappa nevét, és adjon hozzá egy argumentumot, ha a modul nem a projekt gyökerében `<module>`  `--chdir` található. Ha például a  wsgi.py a projekt gyökerében, a *knboard/backend/config* alatt található, használja a `--chdir knboard/backend config.wsgi` argumentumokat.

Az éles naplózás engedélyezéséhez adja hozzá a és paramétereket az egyéni indítási parancsok `--access-logfile` `--error-logfile` [példáiban látható módon.](#customize-startup-command)

### <a name="flask-app"></a>Flask-alkalmazás

A Flask App Service egy application.py vagy  app.py nevű fájlt keres, *és* elindítja a Gunicorn-t a következőképpen:

```bash
# If application.py
gunicorn --bind=0.0.0.0 --timeout 600 application:app

# If app.py
gunicorn --bind=0.0.0.0 --timeout 600 app:app
```

Ha a fő alkalmazásmodul egy másik fájlban található, használjon másik nevet az alkalmazásobjektum számára, vagy ha további argumentumok szeretne megadni a Gunicornhoz, használjon egy [egyéni indítási parancsot](#customize-startup-command).

### <a name="default-behavior"></a>Alapértelmezett viselkedés

Ha a App Service nem talál egyéni parancsot, Django-alkalmazást vagy Flask-alkalmazást, akkor egy alapértelmezett, csak olvasható alkalmazást futtat, amely az _opt/defaultsite_ mappában található, és az alábbi képen látható.

Ha telepítette a kódot, és továbbra is az alapértelmezett alkalmazást látja, tekintse meg [a Hibaelhárítás – Az alkalmazás nem jelenik meg témakört.](#app-doesnt-appear)

[![Alapértelmezett App Service a Linux webhelyen](media/configure-language-python/default-python-app.png)](#app-doesnt-appear)

Ha azt várja, hogy az alapértelmezett alkalmazás helyett egy üzembe helyezett alkalmazás jelenik meg, tekintse meg a Hibaelhárítás [– Az alkalmazás nem jelenik meg témakört.](#app-doesnt-appear)

## <a name="customize-startup-command"></a>Indítási parancs testreszabása

Amint azt a cikk korábbi cikkében említettük, a Gunicorn konfigurációs beállításait *a* projekt gyökerében található gunicorn.conf.py-fájlon keresztül használhatja, a Gunicorn konfigurációjának [áttekintésében leírtak szerint.](https://docs.gunicorn.org/en/stable/configure.html#configuration-file)

Ha ez a konfiguráció nem elegendő, a tároló indítási viselkedését úgy szabályozhatja, hogy egyéni indítási parancsot vagy több parancsot ad meg egy indítási parancsfájlban. Az indítási parancsfájl bármilyen választott nevet használhat, például *startup.sh*, *startup.cmd,* *startup.txt* stb.

Minden parancsnak relatív elérési utakat kell használnia a projekt gyökérmappa felé.

Indítási parancs vagy parancsfájl megadása:

- **Azure Portal:** válassza az alkalmazás Konfiguráció **lapját,** majd válassza az **Általános beállítások lehetőséget.** Az **Indítási parancs mezőben** helyezze el az indítási parancs teljes szövegét vagy az indítási parancsfájl nevét. Ezután válassza **a Mentés lehetőséget** a módosítások alkalmazáshoz. Lásd: [Általános beállítások konfigurálása](configure-common.md#configure-general-settings) Linux-tárolókhoz.

- **Azure CLI:** használja az [az webapp config set parancsot](/cli/azure/webapp/config#az_webapp_config_set) a paraméterrel az indítási parancs vagy fájl `--startup-file` beállítására:

    ```azurecli
    az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
    ```
        
    Cserélje le a helyére az indítási parancs teljes szövegét vagy az indítási `<custom-command>` parancsfájl nevét.
        
App Service figyelmen kívül hagyja az egyéni indítási parancsok vagy fájlok feldolgozásakor előforduló hibákat, majd django- és Flask-alkalmazások használatával folytatja az indítási folyamatot. Ha nem a várt viselkedést látja, ellenőrizze, hogy az indítási parancs vagy fájl hibamentes-e, és hogy a rendszer üzembe helyezett-e egy indítási parancsfájlt az App Service az alkalmazáskóddal együtt. További információt a diagnosztikai [naplókban](#access-diagnostic-logs) is ellenőrizhet. Tekintse meg az alkalmazás Problémák diagnosztizálása és megoldása oldalát **is** a [Azure Portal.](https://portal.azure.com)

### <a name="example-startup-commands"></a>Indítási példaparancsok

- **Gunicorn-argumentumok hozzáadva:** A következő példa hozzáadja a et `--workers=4` egy Gunicorn-parancssorhoz a Django-alkalmazás indításhoz: 

    ```bash
    # <module-path> is the relative path to the folder that contains the module
    # that contains wsgi.py; <module> is the name of the folder containing wsgi.py.
    gunicorn --bind=0.0.0.0 --timeout 600 --workers=4 --chdir <module_path> <module>.wsgi
    ```    

    További információkért lásd: [A Gunicorn futtatása](https://docs.gunicorn.org/en/stable/run.html) (docs.gunicorn.org).

- **Termelési naplózás engedélyezése a Django számára:** Adja hozzá a és az `--access-logfile '-'` `--error-logfile '-'` argumentumot a parancssorhoz:

    ```bash    
    # '-' for the log files means stdout for --access-logfile and stderr for --error-logfile.
    gunicorn --bind=0.0.0.0 --timeout 600 --workers=4 --chdir <module_path> <module>.wsgi --access-logfile '-' --error-logfile '-'
    ```    

    Ezek a naplók a következő [naplóstreamben App Service meg:](#access-diagnostic-logs).

    További információ: [Gunicorn-naplózás](https://docs.gunicorn.org/en/stable/settings.html#logging) (docs.gunicorn.org).
    
- **Custom Flask főmodul:** alapértelmezés szerint a App Service feltételezi, hogy a Flask-alkalmazás fő modulja application.py *vagy* *app.py.* Ha a fő modul más nevet használ, testre kell szabni az indítási parancsot. Tegyük fel például, hogy van egy Flask-alkalmazása, amelynek fő modulja hello.py és a fájl *Flask-alkalmazásobjektumának* neve , akkor a parancs a `myapp` következő:

    ```bash
    gunicorn --bind=0.0.0.0 --timeout 600 hello:myapp
    ```
    
    Ha a főmodul egy almappában található (például `website`), a mappát a `--chdir` argumentummal határozhatja meg:
    
    ```bash
    gunicorn --bind=0.0.0.0 --timeout 600 --chdir website hello:myapp
    ```
    
- **Nem Gunicorn-kiszolgáló** használata: Másik webkiszolgáló, például [az aiohttp](https://aiohttp.readthedocs.io/en/stable/web_quickstart.html)használata esetén használja a megfelelő parancsot indítási parancsként vagy az indítási parancsfájlban:

    ```bash
    python3.7 -m aiohttp.web -H localhost -P 8080 package.module:init_func
    ```

## <a name="access-app-settings-as-environment-variables"></a>Alkalmazásbeállítások elérése környezeti változókként

Az alkalmazásbeállítások olyan értékek, amelyek kifejezetten az alkalmazás számára vannak tárolva a felhőben, az [Alkalmazásbeállítások konfigurálása oldalon leírtak szerint.](configure-common.md#configure-app-settings) Ezek a beállítások környezeti változókként érhetők el az alkalmazáskód számára, és a szabványos [os.environ minta használatával érhetők](https://docs.python.org/3/library/os.html#os.environ) el.

Ha például létrehozta a nevű alkalmazásbeállítást, a következő kód ennek a beállításnak az `DATABASE_SERVER` értékét olvassa be:

```python
db_server = os.environ['DATABASE_SERVER']
```

## <a name="detect-https-session"></a>HTTPS-munkamenet észlelése

Ebben App Service [esetben](https://wikipedia.org/wiki/TLS_termination_proxy) az SSL-leépítés (wikipedia.org) a hálózati terheléselosztásnál történik, így minden HTTPS-kérés titkosítatlan HTTP-kérésként éri el az alkalmazást. Ha az alkalmazáslogikának ellenőriznie kell, hogy a felhasználói kérések titkosítva vannak-e vagy sem, vizsgálja meg a `X-Forwarded-Proto` fejlécet.

```python
if 'X-Forwarded-Proto' in request.headers and request.headers['X-Forwarded-Proto'] == 'https':
# Do something when HTTPS is used
```

A népszerű webes keretrendszerekkel a szokásos `X-Forwarded-*` alkalmazásmintában található információkhoz férhet hozzá. A [CodeIgniterben](https://codeigniter.com/)a [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) alapértelmezés szerint ellenőrzi a `X_FORWARDED_PROTO` értékét.

## <a name="access-diagnostic-logs"></a>Diagnosztikai naplók elérése

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

A naplók eléréséhez Azure Portal alkalmazás bal oldali menüjében válassza a  >  **Monitorozási** naplóstream lehetőséget.

## <a name="access-deployment-logs"></a>Hozzáférés az üzembe helyezési naplókhoz

A kód üzembe helyezésekor a App Service a buildautomatizálás testreszabása című szakaszban korábban ismertetett [buildfolyamatot.](#customize-build-automation) Mivel a build a saját tárolójában fut, a buildnaplókat a rendszer az alkalmazás diagnosztikai naplóitól elkülönítve tárolja.

Az üzembe helyezési naplók eléréséhez kövesse az alábbi lépéseket:

1. A Azure Portal a bal oldali menüben válassza az Üzembe **helyezési** központ  >  **(előzetes verzió)** lehetőséget.
1. A **Naplók lapon** válassza ki a **legutóbbi véglegesítés** véglegesítési azonosítóját.
1. A megjelenő **Napló részletei** lapon válassza a Naplók **megjelenítése...** hivatkozást, amely az "Oryx build futtatása..." mellett jelenik meg.

Ezekben a naplókban  olyan buildhibák jelennek meg, mintrequirements.txtfüggőségek helytelen függőségei és a build előtti vagy utáni szkriptek hibái. Akkor is hibaüzenetek jelennek meg, ha a követelményfájl neve *requirements.txt,* vagy nem jelenik meg a projekt gyökérmappában.

## <a name="open-ssh-session-in-browser"></a>SSH-munkamenet megnyitása böngészőben

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

Ha sikeresen csatlakozott az SSH-munkamenethez, az ablak alján megjelenik az "SSH CONNECTION ESTABLISHED" (SSH-KAPCSOLAT LÉTREJÖTT) üzenet. Ha olyan hibákat lát, mint a "SSH_CONNECTION_CLOSED", vagy egy üzenet arról, hogy a tároló újraindul, előfordulhat, hogy hiba megakadályozza az alkalmazástároló elindulását. A [lehetséges problémák kivizsgálásához](#troubleshooting) tekintse meg a hibaelhárítással kapcsolatos témakört.

## <a name="troubleshooting"></a>Hibaelhárítás

Általánosságban elmondható, hogy a hibaelhárítás első lépése a App Service használata:

1. A Azure Portal a bal oldali menüben válassza a Problémák **diagnosztizálása** és megoldása lehetőséget.
1. Válassza **a Rendelkezésre állás és teljesítmény lehetőséget.**
1. Vizsgálja meg az Alkalmazásnaplók, a  **Tároló összeomlása** és a Tárolók problémái lehetőségeket, ahol a leggyakoribb problémák jelennek meg. 

Ezután vizsgálja meg az üzembe [helyezési naplókat](#access-deployment-logs) és az [alkalmazásnaplókat is,](#access-diagnostic-logs) hogy van-e hibaüzenet. Ezek a naplók gyakran azonosítják azokat a konkrét problémákat, amelyek megakadályozhatják az alkalmazások üzembe helyezését vagy az alkalmazások indítását. A build sikertelen lehet például, *harequirements.txt* fájl rossz fájlnévvel rendelkezik, vagy nincs jelen a projekt gyökérmappában.

A következő szakaszok további útmutatást nyújtanak az egyes problémákhoz.

- [Az alkalmazás nem jelenik meg – az alapértelmezett alkalmazás jelenik meg](#app-doesnt-appear)
- [Az alkalmazás nem jelenik meg – "A szolgáltatás nem érhető el" üzenet](#service-unavailable)
- [Nem található setup.py vagy requirements.txt](#could-not-find-setuppy-or-requirementstxt)
- [ModuleNotFoundError indításkor](#modulenotfounderror-when-app-starts)
- [Az adatbázis zárolva van](#database-is-locked)
- [Begépelt jelszavak nem jelennek meg az SSH-munkamenetben](#other-issues)
- [Úgy tűnik, hogy az SSH-munkamenet parancsai le vannak vágva](#other-issues)
- [A statikus eszközök nem jelennek meg a Django-alkalmazásokban](#other-issues)
- [Végzetes SSL-kapcsolat szükséges](#other-issues)

#### <a name="app-doesnt-appear"></a>Az alkalmazás nem jelenik meg

- **Saját alkalmazáskódjának telepítése után megjelenik az alapértelmezett alkalmazás.** Az [alapértelmezett alkalmazás azért](#default-behavior) jelenik meg, mert vagy nem telepítette az alkalmazáskódot a App Service-be, vagy App Service nem találja az alkalmazáskódot, és futtatta helyette az alapértelmezett alkalmazást.

    - Indítsa újra az App Service-t, várjon 15-20 másodpercet, és ellenőrizze újra az alkalmazást.
    
    - Bizonyosodjon meg róla, hogy az App Service Linux- és nem Windows-alapú példányát használja. Az Azure CLI parancssori felületről futtassa az `az webapp show --resource-group <resource-group-name> --name <app-name> --query kind` parancsot, a `<resource-group-name>` és az `<app-name>` helyőrzőket megfelelően behelyettesítve. Az `app,linux` kimenetet kell látnia, máskülönben hozza újra létre az App Service szolgáltatást, és válassza a linuxos verziót.
    
    - Az [SSH használatával](#open-ssh-session-in-browser) közvetlenül csatlakozhat a App Service tárolóhoz, és ellenőrizheti, hogy a fájlok léteznek-e a *site/wwwroot helyen.* Ha a fájlok nem léteznek, kövesse az alábbi lépéseket:
      1. Hozzon létre egy nevű alkalmazásbeállítást 1 értékkel, újból üzembe kell hoznia a kódot, várjon néhány percet, majd próbálja meg ismét elérni `SCM_DO_BUILD_DURING_DEPLOYMENT` az alkalmazást. További információ az alkalmazásbeállítások létrehozásáról: [Alkalmazásalkalmazás App Service konfigurálása a Azure Portal.](configure-common.md)
      1. Tekintse át az üzembe helyezési folyamatot, ellenőrizze az [üzembe helyezési naplókat,](#access-deployment-logs)javítsa ki az esetleges hibákat, majd az alkalmazás ismételt üzembe helyezését.
    
    - Ha a fájlok léteznek, az App Service nem tudta azonosítani az adott indítási fájlt. Ellenőrizze, hogy az alkalmazás struktúrája megfelel-e annak, amit az App Service a [Django](#django-app) vagy a [Flask](#flask-app) számára elvár, vagy használjon [egyéni indítási parancsot](#customize-startup-command).

- <a name="service-unavailable"></a>**A böngészőben megjelenik a "Service Unavailable" (A szolgáltatás nem érhető el) üzenet.** A böngésző időkorrelációt adott a App Service válasza miatt, ami azt App Service, hogy a App Service elindította a Gunicorn-kiszolgálót, de maga az alkalmazás nem indult el. Ez a feltétel azt jelezheti, hogy a Gunicorn argumentumai helytelenek, vagy hogy hiba van az alkalmazás kódban.

    - Frissítse a böngészőt, különösen akkor, ha az App Service-csomag legalacsonyabb tarifacsomagját használja. Az ingyenes szolgáltatásszintek használatakor például az alkalmazás lassabban indul, és csak a böngésző frissítése után fog ismét reagálni.

    - Ellenőrizze, hogy az alkalmazás struktúrája megfelel-e annak, amit az App Service a [Django](#django-app) vagy a [Flask](#flask-app) számára elvár, vagy használjon [egyéni indítási parancsot](#customize-startup-command).

    - Vizsgálja meg [az alkalmazásnapló-streamben,](#access-diagnostic-logs) hogy van-e hibaüzenet. A naplókban az alkalmazáskódban esetlegesen előforduló hibák fognak mutatni.

#### <a name="could-not-find-setuppy-or-requirementstxt"></a>Nem található setup.py vagy requirements.txt

- **A naplóstream a következőt jeleníti meg: "Nem található setup.py vagy requirements.txt; Nem fut a pip telepítése."**: Az Oryx buildfolyamat nem tudta megtalálni *requirements.txt* fájlt.

    - Csatlakozzon a webalkalmazás tárolóhoz [SSH-n](#open-ssh-session-in-browser) keresztül, és ellenőrizze, hogy arequirements.txthelyesen *van-e* elnevezve, és közvetlenül a *site/wwwroot alatt található-e.* Ha még nem létezik, győződjön meg arról, hogy a fájl létezik az adattárban, és szerepel az üzemelő példányban. Ha külön mappában található, helyezze át a gyökérmappába.

#### <a name="modulenotfounderror-when-app-starts"></a>ModuleNotFoundError az alkalmazás indításakor

Ha ehhez hasonló hibát lát, az azt jelenti, hogy a Python nem talált meg egy vagy több modult az `ModuleNotFoundError: No module named 'example'` alkalmazás miközben elindult. Ez leggyakrabban akkor fordul elő, ha a kóddal együtt telepíti a virtuális környezetet. A virtuális környezetek nem hordozhatók, ezért ne telepítsen virtuális környezetet az alkalmazáskóddal együtt. Ehelyett hagyja, hogy az Oryx létrehoz egy virtuális környezetet, és telepítse a csomagokat a webalkalmazásra egy alkalmazásbeállítás () létrehozásával, majd a `SCM_DO_BUILD_DURING_DEPLOYMENT` `1` beállításával. Ez kényszeríti az Oryx-et, hogy telepítse a csomagokat, amikor üzembe App Service. További információért tekintse meg ezt a cikket a virtuális [környezet hordozhatóságáról.](https://azure.github.io/AppService/2020/12/11/cicd-for-python-apps.html)

### <a name="database-is-locked"></a>Az adatbázis zárolva van

Amikor django-alkalmazással kísérel meg adatbázis-migrálásokat futtatni, az "sqlite3" szó lehet látható. OperationalError: az adatbázis zárolva van." A hiba azt jelzi, hogy az alkalmazás egy olyan SQLite-adatbázist használ, amelyhez a Django alapértelmezés szerint be van állítva, nem pedig egy felhőalapú adatbázist, például az Azure-hoz való PostgreSQL-t használ.

Ellenőrizze az alkalmazás fájljában található változót settings.py, hogy az alkalmazás az `DATABASES` SQLite *helyett* felhőalapú adatbázist használ-e.

Ha ezt a hibát az [Oktatóanyag: Django-webalkalmazás](tutorial-python-postgresql-app.md)üzembe helyezése a PostgreSQL használatával cikkben található mintával találkozik, ellenőrizze, hogy befejezte-e a Környezeti változók konfigurálása az adatbázis csatlakoztatásához [lépéseit.](tutorial-python-postgresql-app.md#42-configure-environment-variables-to-connect-the-database)

#### <a name="other-issues"></a>Egyéb problémák

- A jelszavak nem jelennek meg az **SSH-munkamenetben, amikor be** van gépelve: Biztonsági okokból az SSH-munkamenet elrejti a jelszót a begépelése közben. A rendszer azonban rögzíti a karaktereket, ezért írja be a jelszót a szokásos módon, és ha végzett, nyomja le az **Enter** billentyűt.

- **Úgy tűnik, hogy** az SSH-munkamenetben a parancsok le vannak vágva: Előfordulhat, hogy a szerkesztő nem szóburkoló parancsok, de továbbra is megfelelően kell futniuk.

- **A statikus eszközök nem jelennek meg a Django-alkalmazásokban:** Győződjön meg arról, hogy engedélyezte a [whitenoise modult](http://whitenoise.evans.io/en/stable/django.html)

- **A "Végzetes SSL-kapcsolat szükséges"** üzenet jelenik meg: Ellenőrizze az alkalmazáson belül az erőforrások (például adatbázisok) eléréséhez használt felhasználóneveket és jelszavakat.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Python-alkalmazás És PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Oktatóanyag: Üzembe helyezés privát tárolótárból](tutorial-custom-container.md?pivots=container-linux)

> [!div class="nextstepaction"]
> [App Service a Linuxon – gyakori kérdések](faq-app-service-linux.md)

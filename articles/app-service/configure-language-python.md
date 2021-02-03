---
title: Linux Python-alkalmazások konfigurálása
description: Megtudhatja, hogyan konfigurálhatja a webalkalmazásokat futtató Python-tárolót a Azure Portal és az Azure CLI használatával.
ms.topic: quickstart
ms.date: 02/01/2021
ms.reviewer: astay; kraigb
ms.custom: mvc, seodec18, devx-track-python, devx-track-azurecli
ms.openlocfilehash: 83c49eea8bda10d665c0a08666276e905c60c584
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493702"
---
# <a name="configure-a-linux-python-app-for-azure-app-service"></a>Linux Python-alkalmazás konfigurálása a Azure App Servicehoz

Ez a cikk leírja, hogyan futtatja [Azure app Service](overview.md) a Python-alkalmazásokat, hogyan telepítheti át a meglévő alkalmazásokat az Azure-ba, és hogyan szabhatja testre a app Service viselkedését, ha szükséges. A Python-alkalmazásokat az összes szükséges [pip](https://pypi.org/project/pip/) -modullal telepíteni kell.

A App Service üzembe helyezési motor automatikusan aktiválja a virtuális környezetet, és `pip install -r requirements.txt` a [git-tárház](deploy-local-git.md)vagy egy zip- [csomag](deploy-zip.md)telepítésekor futtatja azt.

Ez az útmutató ismerteti a Python-fejlesztők számára a App Service beépített Linux-tárolóját használó főbb fogalmakat és útmutatást. Ha még soha nem használta a Azure App Servicet, először kövesse a [Python](quickstart-python.md) rövid [útmutatóját és a Pythont a PostgreSQL oktatóanyaggal](tutorial-python-postgresql-app.md).

A konfiguráláshoz használhatja a [Azure Portal](https://portal.azure.com) vagy az Azure CLI-t:

- **Azure Portal** használja az alkalmazás **Beállítások**  >  **konfigurációs** lapját az [Azure Portal app Service alkalmazás konfigurálása](configure-common.md)című cikkben leírtak szerint.

- **Azure CLI**: két lehetőség közül választhat.

    - Futtassa a parancsokat a [Azure Cloud Shell](../cloud-shell/overview.md).
    - Futtassa a parancsokat helyileg az [Azure CLI](/cli/azure/install-azure-cli)legújabb verziójának telepítésével, majd jelentkezzen be az Azure-ba az [az login](/cli/azure/reference-index#az-login)használatával.
    
> [!NOTE]
> A Linux jelenleg az ajánlott lehetőség a Python-alkalmazások App Service-ben való futtatására. További információ a Windows lehetőségről: [Python a app Service Windows-íz](/visualstudio/python/managing-python-on-azure-app-service).

## <a name="configure-python-version"></a>Python-verzió konfigurálása

- **Azure Portal**: használja a **konfiguráció** lap **általános beállítások** lapját a Linux-tárolók [általános beállításainak konfigurálása](configure-common.md#configure-general-settings) című témakörben leírtak szerint.

- **Azure CLI**:

    -  Az aktuális Python-verzió megjelenítése az [az WebApp config show paranccsal](/cli/azure/webapp/config#az_webapp_config_show):
    
        ```azurecli
        az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
        ```
        
        Cserélje `<resource-group-name>` le `<app-name>` a és a nevet a webalkalmazásának megfelelő nevekre.
    
    - A Python verziójának beállítása az [WebApp config set](/cli/azure/webapp/config#az_webapp_config_set)
        
        ```azurecli
        az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "PYTHON|3.7"
        ```
    
    - A Azure App Service által támogatott összes Python-verzió megjelenítése az az [WebApp List-Runtimes](/cli/azure/webapp#az_webapp_list_runtimes)paranccsal:
    
        ```azurecli
        az webapp list-runtimes --linux | grep PYTHON
        ```
    
A Python nem támogatott verzióját a saját tároló rendszerképének létrehozásával futtathatja. További információ: [egyéni Docker-rendszerkép használata](tutorial-custom-container.md?pivots=container-linux).

<!-- <a> element here to preserve external links-->
<a name="access-environment-variables"></a>

## <a name="customize-build-automation"></a>A Build Automation testreszabása

App Service a Oryx nevű Build-rendszer a következő lépéseket hajtja végre az alkalmazás git vagy zip-csomagok használatával történő telepítésekor:

1. Ha ezt a beállítást adja meg, futtasson egyéni, előkészítő parancsfájlt `PRE_BUILD_COMMAND` . (A szkript saját maga is futtathat más Python-és Node.js-parancsfájlokat, Pip-és NPM-parancsokat, valamint olyan csomópont-alapú eszközöket, mint a fonal, például a `yarn install` és a `yarn build` .)

1. Futtassa a `pip install -r requirements.txt` parancsot. A *requirements.txt* fájlnak jelen kell lennie a projekt gyökérkönyvtárában. Ellenkező esetben a fordítási folyamat a következő hibát jelenti: "nem található a setup.py vagy a requirements.txt; Nem fut a pip telepítése. "

1. Ha a *Manage.py* a tárház gyökerében található (Django-alkalmazást jelez), futtassa a *Manage.py collectstatic*. Ha azonban ez a `DISABLE_COLLECTSTATIC` beállítás `true` , ez a lépés kimarad.

1. Ha a beállítás megadja az egyéni létrehozás utáni parancsfájlt, `POST_BUILD_COMMAND` (A szkript a Python-és Node.js-parancsfájlok, a pip-és a NPM-parancsok, valamint a node-alapú eszközök futtatására is használható.)

Alapértelmezés szerint a `PRE_BUILD_COMMAND` , a `POST_BUILD_COMMAND` és a `DISABLE_COLLECTSTATIC` Beállítások üresek. 

- Ha le szeretné tiltani a collectstatic futtatását a Django-alkalmazások létrehozásakor, állítsa a `DISABLE_COLLECTSTATIC` beállítást igaz értékre.

- Az előkészítő parancsok futtatásához állítsa a `PRE_BUILD_COMMAND` beállítást úgy, hogy az a `echo Pre-build command` projekt gyökeréhez képest egy parancsot (például) vagy egy parancsfájl elérési útját (például) tartalmazza `scripts/prebuild.sh` . Minden parancsnak relatív elérési utakat kell használnia a projekt gyökérkönyvtárához.

- A létrehozás utáni parancsok futtatásához állítsa a `POST_BUILD_COMMAND` beállítást úgy, hogy a a `echo Post-build command` projekt gyökeréhez képest egy parancsot (például) vagy egy parancsfájl elérési útját (például) tartalmazza `scripts/postbuild.sh` . Minden parancsnak relatív elérési utakat kell használnia a projekt gyökérkönyvtárához.

A Build Automation testreszabásával kapcsolatos további beállításokért lásd: [Oryx-konfiguráció](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md). 

A build és a központi telepítési naplók eléréséhez lásd: [hozzáférés a központi telepítési naplókhoz](#access-deployment-logs).

A Python-alkalmazások Linux rendszeren való futtatásával és a App Serviceával kapcsolatos további információkért lásd: [how Oryx észleli és létrehozza a Python-alkalmazásokat](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/python.md).

> [!NOTE]
> A és a beállítások azonosak a és a, és az `PRE_BUILD_SCRIPT_PATH` `POST_BUILD_SCRIPT_PATH` `PRE_BUILD_COMMAND` `POST_BUILD_COMMAND` örökölt célokra támogatottak.
> 
> `SCM_DO_BUILD_DURING_DEPLOYMENT`Ha A (z) vagy 1 értéket tartalmaz, a rendszer elindítja a Oryx- `true` buildet az üzembe helyezés során. A beállítás igaz a git, az Azure CLI-parancs `az webapp up` és a Visual Studio Code használatával történő üzembe helyezéskor.

> [!NOTE]
> Mindig relatív elérési utakat használjon az összes előre és post-Build parancsfájlban, mert az a Build-tároló, amelyben a Oryx fut, eltér az alkalmazást futtató futtatókörnyezeti tárolótól. Soha ne támaszkodjon az alkalmazás Project mappájának a tárolón belüli pontos elhelyezésére (például úgy, hogy az a *site/wwwroot* alá van helyezve).

## <a name="migrate-existing-applications-to-azure"></a>Meglévő alkalmazások migrálása az Azure-ba

A meglévő webalkalmazások a következőképpen helyezhetők üzembe az Azure-ban:

1. **Forrás adattár**: a forráskódot egy megfelelő tárházban, például a githubban tarthatja karban, amely lehetővé teszi a folyamatos üzembe helyezést a folyamat későbbi részében.
    1. A *requirements.txt* -fájlnak a tárház gyökerében kell lennie, app Service a szükséges csomagok automatikus telepítéséhez.    

1. **Adatbázis**: Ha az alkalmazás egy adatbázistól függ, kiépítheti a szükséges erőforrásokat az Azure-ban is. Lásd [: oktatóanyag: Django-webalkalmazás üzembe helyezése PostgreSQL-ben – adatbázis létrehozása](tutorial-python-postgresql-app.md#3-create-postgres-database-in-azure) egy példához.

1. **App Service-erőforrások**: hozzon létre egy erőforráscsoportot, app Service tervet, és app Service webalkalmazást az alkalmazás üzemeltetéséhez. Ezt egyszerűen megteheti a kód első üzembe helyezésével az Azure CLI `az webapp up` -paranccsal, ahogy az [oktatóanyag: Django-webalkalmazás üzembe helyezése a PostgreSQL-ben – a kód üzembe helyezése](tutorial-python-postgresql-app.md#4-deploy-the-code-to-azure-app-service). Cserélje le az erőforráscsoport nevét, App Service tervet, és a webalkalmazást, hogy jobban megfeleljen az alkalmazásnak.

1. **Környezeti változók**: Ha az alkalmazásnak környezeti változókra van szüksége, hozzon létre egyenértékű [app Service Alkalmazásbeállítások](configure-common.md#configure-app-settings). Ezek a App Service beállítások környezeti változókként jelennek meg a kódban, a [hozzáférési környezet változói](#access-app-settings-as-environment-variables)című témakörben leírtak szerint.
    - Az adatbázis-kapcsolatok például gyakran az ilyen beállításokon keresztül kezelhetők, ahogy az [oktatóanyag: Django-webalkalmazás telepítése PostgreSQL-ben – változók konfigurálása az adatbázis csatlakoztatására című útmutatóban](tutorial-python-postgresql-app.md#42-configure-environment-variables-to-connect-the-database)látható.
    - Tekintse meg a [Django-alkalmazások üzemi beállításait](#production-settings-for-django-apps) a tipikus Django-alkalmazások adott beállításaihoz.

1. **Alkalmazás indítása**: a cikk későbbi részében tekintse át a [tároló indítási folyamatát](#container-startup-process) , hogy megtudja, hogyan app Service próbálja futtatni az alkalmazást. A App Service alapértelmezés szerint a Gunicorn webkiszolgálót használja, amely képesnek kell lennie az alkalmazás-objektum vagy a *WSGI.py* mappa megkeresésére. Ha szükséges, testre is [szabhatja az indítási parancsot](#customize-startup-command).

1. **Folyamatos üzembe helyezés**: állítsa be a folyamatos üzembe helyezést a folyamatos üzembe [helyezésről Azure app Service](deploy-continuous-deployment.md) ha Azure-folyamatokat vagy kudu üzembe helyezést használ, vagy ha GitHub-műveleteket használ a GitHub-műveletek használatával [történő app Service](deploy-github-actions.md) .

1. **Egyéni műveletek**: az alkalmazást futtató app Service tárolón belüli műveletek végrehajtásához, például a Django-adatbázis áttelepítéséhez, SSH-n [keresztül csatlakozhat a tárolóhoz](configure-linux-open-ssh-session.md). A Django-adatbázis áttelepítésének futtatására példát a következő témakörben talál [: oktatóanyag: Django-webalkalmazás üzembe helyezése PostgreSQL-ben – adatbázis-áttelepítés futtatása](tutorial-python-postgresql-app.md#43-run-django-database-migrations).
    - Folyamatos üzembe helyezés esetén ezeket a műveleteket a [Build Automation testreszabása](#customize-build-automation)című szakaszban leírtak szerint végezheti el a létrehozás utáni parancsokkal.

Ezeknek a lépéseknek a végrehajtásával véglegesítheti a forrás-adattár módosításait, és automatikusan telepítheti ezeket a frissítéseket a App Service.

### <a name="production-settings-for-django-apps"></a>Django-alkalmazások üzemi beállításai

Az olyan éles környezetekhez, mint a Azure App Service, a Django-alkalmazásoknak a Django [üzembe helyezési ellenőrzőlistáját](https://docs.djangoproject.com/en/3.1/howto/deployment/checklist/) (djangoproject.com) kell követniük.

Az alábbi táblázat az Azure-hoz kapcsolódó éles beállításokat ismerteti. Ezek a beállítások az alkalmazás *Setting.py* -fájljában vannak meghatározva.

| Django-beállítás | Azure-utasítások |
| --- | --- |
| `SECRET_KEY` | Tárolja az értéket egy App Service-beállításban az [Access app Settings as környezeti változókként](#access-app-settings-as-environment-variables)leírt módon. Az értéket felválthatja ["secreting"-ként a Azure Key Vault](../key-vault/secrets/quick-create-python.md). |
| `DEBUG` | Hozzon létre egy `DEBUG` beállítást a app Service a 0 (false) értékkel, majd töltse be az értéket környezeti változóként. A fejlesztési környezetében hozzon létre egy `DEBUG` környezeti változót az 1 (igaz) értékkel. |
| `ALLOWED_HOSTS` | Éles környezetben a Django megköveteli, hogy az alkalmazás URL-címét tartalmazza a `ALLOWED_HOSTS` *Settings.py* tömbben. Ezt az URL-címet futásidőben kérheti le a kóddal `os.environ['WEBSITE_HOSTNAME']` . App Service automatikusan beállítja a `WEBSITE_HOSTNAME` környezeti változót az alkalmazás URL-címére. |
| `DATABASES` | Adja meg az adatbázis-kapcsolatok App Service beállításait, és töltse be azokat környezeti változókként a szótár feltöltéséhez [`DATABASES`](https://docs.djangoproject.com/en/3.1/ref/settings/#std:setting-DATABASES) . Az értékeket (különösen a felhasználónevet és a jelszót) a [Azure Key Vault titokként](../key-vault/secrets/quick-create-python.md)is tárolhatja. |

## <a name="serve-static-files-for-django-apps"></a>Statikus fájlok kiszolgálása Django-alkalmazásokhoz

Ha a Django-webalkalmazás statikus előtér-fájlokat tartalmaz, először kövesse a Django dokumentációjában található [statikus fájlok kezelésével](https://docs.djangoproject.com/en/3.1/howto/static-files/) kapcsolatos utasításokat.

A App Service a következő módosításokat hajtja végre:

1. A Django és a változók dinamikus beállításához érdemes lehet környezeti változókat (helyi fejlesztés esetén) és Alkalmazásbeállítások (a felhőbe való üzembe helyezéskor) használni `STATIC_URL` `STATIC_ROOT` . Például:    

    ```python
    STATIC_URL = os.environ.get("DJANGO_STATIC_URL", "/static/")
    STATIC_ROOT = os.environ.get("DJANGO_STATIC_ROOT", "./static/")    
    ```

    `DJANGO_STATIC_URL` a `DJANGO_STATIC_ROOT` helyi és a Felhőbeli környezetekhez szükség szerint módosítható. Ha például a statikus fájlok létrehozási folyamata egy nevű mappába helyezi őket `django-static` , akkor a `DJANGO_STATIC_URL` gombra kattintva `/django-static/` elkerülheti az alapértelmezett beállítást.

1. Ha olyan előzetes Build parancsfájllal rendelkezik, amely egy másik mappában statikus fájlokat hoz létre, akkor a Django változóban adja meg a mappát, `STATICFILES_DIRS` hogy a Django `collectstatic` folyamata megkeresse őket. Ha például az `yarn build` előtér-mappában fut, és a fonal létrehoz egy `build/static` statikus fájlokat tartalmazó mappát, a következő módon adja meg az adott mappát:

    ```python
    FRONTEND_DIR = "path-to-frontend-folder" 
    STATICFILES_DIRS = [os.path.join(FRONTEND_DIR, 'build', 'static')]    
    ```

    Itt `FRONTEND_DIR` egy olyan elérési utat hozhat létre, ahol egy Build eszköz, például a szál fut. A környezeti változót és az alkalmazás beállításait igény szerint is használhatja.

1. Adja hozzá `whitenoise` a *requirements.txt* -fájlhoz. A [Whitenoise](http://whitenoise.evans.io/en/stable/) (whitenoise.Evans.IO) egy Python-csomag, amely egyszerűvé teszi az éles Django-alkalmazások számára a saját statikus fájlok kiszolgálását. A Whitenoise kifejezetten azokat a fájlokat szolgálja ki, amelyek a Django változó által megadott mappában találhatók `STATIC_ROOT` .

1. A *Settings.py* -fájlban adja hozzá a következő sort a Whitenoise:

    ```python
    STATICFILES_STORAGE = ('whitenoise.storage.CompressedManifestStaticFilesStorage')
    ```

1. Módosítsa a `MIDDLEWARE` és a `INSTALLED_APPS` listát is, hogy tartalmazza a Whitenoise:

    ```python
    MIDDLEWARE = [
        "whitenoise.middleware.WhiteNoiseMiddleware",
        # Other values follow
    ]

    INSTALLED_APPS = [
        "whitenoise.runserver_nostatic",
        # Other values follow
    ]
    ```

## <a name="container-characteristics"></a>A tároló jellemzői

App Service rendszerbe való üzembe helyezéskor a Python-alkalmazások egy, a [app Service Python GitHub-tárházban](https://github.com/Azure-App-Service/python)meghatározott Linux Docker-tárolón belül futnak. A rendszerkép-konfigurációk a verziószámozási könyvtárakban találhatók.

Ez a tároló a következő jellemzőkkel rendelkezik:

- Az alkalmazások a [Gunicorn WSGI HTTP-kiszolgáló](https://gunicorn.org/) és további argumentumok használatával futnak`--bind=0.0.0.0 --timeout 600`.
    - A Gunicorn konfigurációs beállításait megadhatja a *gunicorn.conf.py* -fájlon keresztül a [Gunicorn-konfiguráció áttekintése](https://docs.gunicorn.org/en/stable/configure.html#configuration-file) (docs.gunicorn.org) című cikkben leírtak szerint. Lehetőség van [az indítási parancs testreszabására](#customize-startup-command)is.

    - A webalkalmazások véletlen vagy szándékos DDOS-támadások elleni védelem érdekében a Gunicorn egy Nginx fordított proxy mögött fut a Gunicorn (docs.gunicorn.org) [üzembe helyezése](https://docs.gunicorn.org/en/latest/deploy.html) című témakörben leírtak szerint.

- Alapértelmezés szerint az alapszintű tároló képe csak a lombik webes keretrendszerét tartalmazza, a tároló azonban támogatja a WSGI-kompatibilis és a Python 3.6 + szabvánnyal kompatibilis más keretrendszerek használatát, például a Django-t.

- További csomagok, például a Django telepítéséhez hozzon létre egy [*requirements.txt*](https://pip.pypa.io/en/stable/user_guide/#requirements-files) fájlt a projekt gyökérkönyvtárában, amely meghatározza a közvetlen függőségeket. A App Service ezután automatikusan telepíti ezeket a függőségeket a projekt központi telepítésekor.

    A telepítendő függőségekhez a *requirements.txt* fájlnak a projekt gyökerében *kell* lennie. Ellenkező esetben a fordítási folyamat a következő hibát jelenti: "nem található a setup.py vagy a requirements.txt; Nem fut a pip telepítése. " Ha ezt a hibát tapasztalja, ellenőrizze a követelmények fájljának helyét.

- App Service automatikusan definiál egy nevű környezeti változót `WEBSITE_HOSTNAME` a webalkalmazás URL-címével, például a következővel: `msdocs-hello-world.azurewebsites.net` . Emellett `WEBSITE_SITE_NAME` az alkalmazás nevét is meghatározza, például: `msdocs-hello-world` . 
   
- a NPM és a Node.js a tárolóba vannak telepítve, így a node-alapú Build-eszközöket, például a fonalat is futtathatja.

## <a name="container-startup-process"></a>Tároló indítási folyamata

Rendszerindítás során a Linux-tárolóban lévő App Service a következő lépéseket futtatja:

1. Ha meg van adni, használjon [Egyéni indítási parancsot](#customize-startup-command).
2. Ellenőrizze, hogy létezik-e [Django alkalmazás](#django-app), és ha észlelte, indítsa el a Gunicorn.
3. Ellenőrizze, hogy létezik-e a [lombik-alkalmazás](#flask-app), és ha észlelt, indítsa el a Gunicorn.
4. Ha más alkalmazás nem található, indítson el egy alapértelmezett alkalmazást, amely a tárolóba van beépítve.

A következő szakaszok további információkkal szolgálnak az egyes beállításokról.

### <a name="django-app"></a>Django-alkalmazás

Django-alkalmazások esetén az App Service a(z) `wsgi.py` nevű fájlt keresi az alkalmazáskódban, majd a Gunicorn futtatásához a következő parancsot használja:

```bash
# <module> is the name of the folder that contains wsgi.py
gunicorn --bind=0.0.0.0 --timeout 600 <module>.wsgi
```

Ha pontosabban szeretné vezérelni az indítási parancsot, használjon [Egyéni indítási parancsot](#customize-startup-command), cserélje le `<module>` a nevet a *WSGI.py* tartalmazó mappa nevére, és adjon hozzá egy `--chdir` argumentumot, ha a modul nem szerepel a projekt gyökerében. Ha például a *WSGI.py* a *knboard/háttér/konfiguráció* területen található a projekt gyökerénél, használja az argumentumokat `--chdir knboard/backend config.wsgi` .

Az éles naplózás engedélyezéséhez adja hozzá a `--access-logfile` és a `--error-logfile` paramétereket az [Egyéni indítási parancsokra](#customize-startup-command)vonatkozó példákban látható módon.

### <a name="flask-app"></a>Flask-alkalmazás

A lombik esetében App Service egy *Application.py* vagy *app.py* nevű fájlt keres, és elindítja a Gunicorn a következőképpen:

```bash
# If application.py
gunicorn --bind=0.0.0.0 --timeout 600 application:app

# If app.py
gunicorn --bind=0.0.0.0 --timeout 600 app:app
```

Ha a fő alkalmazásmodul egy másik fájlban található, használjon másik nevet az alkalmazásobjektum számára, vagy ha további argumentumok szeretne megadni a Gunicornhoz, használjon egy [egyéni indítási parancsot](#customize-startup-command).

### <a name="default-behavior"></a>Alapértelmezett viselkedés

Ha a App Service nem talál egyéni parancsot, egy Django-alkalmazást vagy egy lombik-alkalmazást, akkor egy alapértelmezett írásvédett alkalmazást futtat, amely az _opt/defaultsite_ mappában található, és az alábbi képen látható.

Ha központilag telepítette a kódot, és továbbra is az alapértelmezett alkalmazást látja, tekintse [meg a hibaelhárítás – alkalmazás nem jelenik meg](#app-doesnt-appear).

[![Alapértelmezett App Service a Linux webhelyen](media/configure-language-python/default-python-app.png)](#app-doesnt-appear)

Ha az alapértelmezett alkalmazás helyett egy telepített alkalmazást szeretne látni, tekintse [meg a hibaelhárítás – alkalmazás nem jelenik meg](#app-doesnt-appear).

## <a name="customize-startup-command"></a>Indítási parancs testreszabása

A cikkben korábban leírtaknak megfelelően a Gunicorn konfigurációs beállításait a *gunicorn.conf.py* -fájlon keresztül is megadhatja a projekt gyökerében, a [Gunicorn-konfiguráció áttekintése](https://docs.gunicorn.org/en/stable/configure.html#configuration-file)című témakörben leírtak szerint.

Ha ez a konfiguráció nem elegendő, a tároló indítási viselkedését beállíthatja úgy, hogy egyéni indítási parancsot vagy több parancsot indít el egy indítási parancsfájlban. Az indítási parancsfájl tetszőleges nevet használhat, például *Startup.sh*, *Startup. cmd*, *startup.txt* stb.

Minden parancsnak relatív elérési utakat kell használnia a projekt gyökérkönyvtárához.

Indítási parancs vagy parancsfájl megadásához:

- **Azure Portal**: válassza ki az alkalmazás **konfiguráció** lapját, majd válassza az **általános beállítások** lehetőséget. Az **indítási parancs** mezőben helyezze el az indítási parancs teljes szövegét vagy az indítási parancsfájl nevét. Ezután kattintson a **Mentés** gombra a módosítások alkalmazásához. Lásd: a Linux-tárolók [általános beállításainak konfigurálása](configure-common.md#configure-general-settings) .

- **Azure CLI**: használja az az [WebApp config set](/cli/azure/webapp/config#az_webapp_config_set) parancsot a `--startup-file` paraméterrel az indítási parancs vagy fájl beállításához:

    ```azurecli
    az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
    ```
        
    A helyére írja `<custom-command>` be az indítási parancs teljes szövegét vagy az indítási parancsfájl nevét.
        
App Service figyelmen kívül hagyja az egyéni indítási parancsok vagy fájlok feldolgozásakor előforduló hibákat, majd a Django és a lombik alkalmazások keresésével folytatja az indítási folyamatát. Ha nem látja a várt viselkedést, ellenőrizze, hogy az indítási parancs vagy a fájl hibamentes-e, és hogy az alkalmazás kódjával együtt App Service-e az indítási parancsfájl. További információkért tekintse meg a [diagnosztikai naplókat](#access-diagnostic-logs) is. Tekintse meg az alkalmazás **diagnosztizálási és megoldási problémáit** ismertető oldalt is a [Azure Portal](https://portal.azure.com).

### <a name="example-startup-commands"></a>Példa indítási parancsokra

- **Hozzáadott Gunicorn argumentumai**: a következő példa hozzáadja a `--workers=4` -t egy Gunicorn parancssorhoz egy Django-alkalmazás indításához: 

    ```bash
    # <module-path> is the relative path to the folder that contains the module
    # that contains wsgi.py; <module> is the name of the folder containing wsgi.py.
    gunicorn --bind=0.0.0.0 --timeout 600 --workers=4 --chdir <module_path> <module>.wsgi
    ```    

    További információkért lásd: [A Gunicorn futtatása](https://docs.gunicorn.org/en/stable/run.html) (docs.gunicorn.org).

- A **Django éles naplózásának engedélyezése**: adja hozzá a (z) `--access-logfile '-'` és `--error-logfile '-'` argumentumokat a parancssorhoz:

    ```bash    
    # '-' for the log files means stdout for --access-logfile and stderr for --error-logfile.
    gunicorn --bind=0.0.0.0 --timeout 600 --workers=4 --chdir <module_path> <module>.wsgi --access-logfile '-' --error-logfile '-'
    ```    

    Ezek a naplók a [app Service log streamben](#access-diagnostic-logs)fognak megjelenni.

    További információ: Gunicorn- [naplózás](https://docs.gunicorn.org/en/stable/settings.html#logging) (docs.gunicorn.org).
    
- **Egyéni lombik főmodulja**: alapértelmezés szerint a app Service feltételezi, hogy a lombik-alkalmazás fő modulja a *application.py* vagy a *app.py*. Ha a fő modul más nevet használ, testre kell szabnia az indítási parancsot. Például YF van egy olyan lombik-alkalmazás, amelynek a fő modulja *Hello.py* , és az abban a fájlban található lombik alkalmazás-objektum neve `myapp` , a parancs a következő:

    ```bash
    gunicorn --bind=0.0.0.0 --timeout 600 hello:myapp
    ```
    
    Ha a főmodul egy almappában található (például `website`), a mappát a `--chdir` argumentummal határozhatja meg:
    
    ```bash
    gunicorn --bind=0.0.0.0 --timeout 600 --chdir website hello:myapp
    ```
    
- **Nem Gunicorn-kiszolgáló** használata: Ha másik webkiszolgálót (például [aiohttp](https://aiohttp.readthedocs.io/en/stable/web_quickstart.html)) szeretne használni, használja a megfelelő parancsot indítási parancsként vagy az indítási parancsfájlban:

    ```bash
    python3.7 -m aiohttp.web -H localhost -P 8080 package.module:init_func
    ```

## <a name="access-app-settings-as-environment-variables"></a>Alkalmazás beállításainak elérése környezeti változókként

Az Alkalmazásbeállítások az alkalmazás [beállításainak konfigurálása](configure-common.md#configure-app-settings)című részben leírtaknak megfelelően a felhőben tárolt értékek. Ezek a beállítások környezeti változókként érhetők el az alkalmazás kódjában, és a szabványos [operációs rendszer. környezettel](https://docs.python.org/3/library/os.html#os.environ) érhetők el.

Ha például létrehozta az Alkalmazásbeállítás nevű alkalmazást `DATABASE_SERVER` , a következő kód lekéri a beállítás értékét:

```python
db_server = os.environ['DATABASE_SERVER']
```

## <a name="detect-https-session"></a>HTTPS-munkamenet észlelése

App Service az [SSL-lezárás](https://wikipedia.org/wiki/TLS_termination_proxy) (wikipedia.org) a hálózati terheléselosztó esetében történik, így minden HTTPS-kérelem titkosítatlan http-kérésként éri el az alkalmazást. Ha az alkalmazás logikájának ellenőriznie kell, hogy a felhasználói kérések titkosítva vannak-e, vagy sem, vizsgálja meg a `X-Forwarded-Proto` fejlécet.

```python
if 'X-Forwarded-Proto' in request.headers and request.headers['X-Forwarded-Proto'] == 'https':
# Do something when HTTPS is used
```

A népszerű webes keretrendszerek lehetővé teszik a `X-Forwarded-*` szabványos alkalmazási mintában lévő információk elérését. A [CodeIgniter](https://codeigniter.com/)-ben a [is_https ()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) alapértelmezés szerint ellenőrzi a értéket `X_FORWARDED_PROTO` .

## <a name="access-diagnostic-logs"></a>Diagnosztikai naplók elérése

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

A naplók a Azure Portalon keresztüli eléréséhez válassza a **figyelés**  >  **napló stream** lehetőséget az alkalmazás bal oldali menüjében.

## <a name="access-deployment-logs"></a>Központi telepítési naplók elérése

A kód központi telepítésekor a App Service végrehajtja a [Build Automation testreszabása](#customize-build-automation)című szakaszban ismertetett fordítási folyamatot. Mivel a Build a saját tárolójában fut, a Build-naplókat az alkalmazás diagnosztikai naplóitól függetlenül tárolja a rendszer.

A telepítési naplók eléréséhez kövesse az alábbi lépéseket:

1. A webalkalmazás Azure Portal a bal oldali menüben válassza a **központi** telepítési  >  **központ (előzetes verzió)** lehetőséget.
1. A **naplók** lapon válassza ki a legutóbbi végrehajtáshoz tartozó **véglegesítő azonosítót** .
1. A megjelenő **napló részletei** lapon válassza a **naplók megjelenítése...** hivatkozást, amely a "Futtatás Oryx Build..." mellett jelenik meg.

Ezekben a naplókban olyan problémákat hozhat létre, mint például a helytelen függőségek a *requirements.txtban* , és az előre vagy post-Build parancsfájlokban előforduló hibák is megjelennek. A hibák akkor is megjelennek, ha a követelmények fájlja nem pontosan *requirements.txt* , vagy nem jelenik meg a projekt gyökérkönyvtárában.

## <a name="open-ssh-session-in-browser"></a>SSH-munkamenet megnyitása böngészőben

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

Ha sikeresen csatlakozott az SSH-munkamenethez, az ablak alján található "SSH-kapcsolat létrejött" üzenet jelenik meg. Ha olyan hibák jelennek meg, mint például a "SSH_CONNECTION_CLOSED" vagy egy üzenet, amely a tároló újraindítását eredményezi, akkor hiba lehet az alkalmazás-tároló indításának megakadályozása. A lehetséges problémák kivizsgálásához szükséges lépésekért lásd: [Hibaelhárítás](#troubleshooting) .

## <a name="troubleshooting"></a>Hibaelhárítás

Általánosságban elmondható, hogy a hibaelhárítás első lépése a App Service diagnosztika használata:

1. A webalkalmazás Azure Portal a bal oldali menüben válassza a **diagnosztizálás és problémák megoldása** lehetőséget.
1. Válassza ki **a rendelkezésre állást és a teljesítményt**.
1. Vizsgálja meg az **alkalmazás naplói**, a **tároló összeomlása** és a **tárolókkal kapcsolatos problémák** lehetőségeit, ahol a leggyakoribb problémák jelennek meg.

Ezután vizsgálja meg a [telepítési naplókat](#access-deployment-logs) és az [alkalmazás naplófájljait](#access-diagnostic-logs) is a hibaüzenetek esetében. Ezek a naplók gyakran azonosítják az alkalmazások központi telepítésének vagy az alkalmazások indításának megakadályozására szolgáló konkrét problémákat. A Build sikertelen lehet például, ha a *requirements.txt* fájljának helytelen a neve, vagy nincs jelen a projekt gyökérkönyvtárában.

A következő szakaszokban további útmutatást talál az adott problémákkal kapcsolatban.

- [Az alkalmazás nem jelenik meg – az alapértelmezett alkalmazás megjelenítése](#app-doesnt-appear)
- [Az alkalmazás nem jelenik meg – a "szolgáltatás nem érhető el" üzenet](#service-unavailable)
- [Nem található setup.py vagy requirements.txt](#could-not-find-setuppy-or-requirementstxt)
- [A jelszó nem jelenik meg az SSH-munkamenetben, ha be van írva](#other-issues)
- [Úgy tűnik, hogy az SSH-munkamenetben lévő parancsok ki lesznek vágva](#other-issues)
- [A statikus eszközök nem jelennek meg a Django alkalmazásban](#other-issues)
- [Végzetes SSL-kapcsolat szükséges](#other-issues)

#### <a name="app-doesnt-appear"></a>Az alkalmazás nem jelenik meg

- **Saját alkalmazáskódjának telepítése után megjelenik az alapértelmezett alkalmazás.** Az [alapértelmezett alkalmazás](#default-behavior) jelenik meg, mert vagy nem telepítette az alkalmazás kódját app Servicere, vagy app Service nem találta meg az alkalmazás kódját, és nem futtatta az alapértelmezett alkalmazást.

    - Indítsa újra az App Service-t, várjon 15-20 másodpercet, és ellenőrizze újra az alkalmazást.
    
    - Bizonyosodjon meg róla, hogy az App Service Linux- és nem Windows-alapú példányát használja. Az Azure CLI parancssori felületről futtassa az `az webapp show --resource-group <resource-group-name> --name <app-name> --query kind` parancsot, a `<resource-group-name>` és az `<app-name>` helyőrzőket megfelelően behelyettesítve. Az `app,linux` kimenetet kell látnia, máskülönben hozza újra létre az App Service szolgáltatást, és válassza a linuxos verziót.
    
    - Az [SSH](#open-ssh-session-in-browser) használatával csatlakozzon közvetlenül a app Service tárolóhoz, és ellenőrizze, hogy a fájlok léteznek-e a *site/wwwroot* alatt. Ha a fájlok nem léteznek, kövesse az alábbi lépéseket:
      1. Hozzon létre egy nevű alkalmazás `SCM_DO_BUILD_DURING_DEPLOYMENT` -beállítást az 1 értékkel, telepítse újra a kódot, várjon néhány percet, majd próbálja meg újra elérni az alkalmazást. Az Alkalmazásbeállítások létrehozásával kapcsolatos további információkért lásd: [app Service alkalmazás konfigurálása a Azure Portalban](configure-common.md).
      1. Tekintse át a telepítési folyamatot, [ellenőrizze a központi telepítési naplókat](#access-deployment-logs), javítsa ki a hibákat, és telepítse újra az alkalmazást.
    
    - Ha a fájlok léteznek, az App Service nem tudta azonosítani az adott indítási fájlt. Ellenőrizze, hogy az alkalmazás struktúrája megfelel-e annak, amit az App Service a [Django](#django-app) vagy a [Flask](#flask-app) számára elvár, vagy használjon [egyéni indítási parancsot](#customize-startup-command).

- <a name="service-unavailable"></a>**A "szolgáltatás nem érhető el" üzenet jelenik meg a böngészőben.** A böngésző túllépte az időkorlátot, miközben a App Servicetól érkező választ várt, ami azt jelzi, hogy App Service elindította a Gunicorn-kiszolgálót, de maga az alkalmazás nem indult el. Ez az állapot azt jelezheti, hogy a Gunicorn argumentumok helytelenek, vagy ha hiba van az alkalmazás kódjában.

    - Frissítse a böngészőt, különösen akkor, ha az App Service-csomag legalacsonyabb tarifacsomagját használja. Az ingyenes szolgáltatásszintek használatakor például az alkalmazás lassabban indul, és csak a böngésző frissítése után fog ismét reagálni.

    - Ellenőrizze, hogy az alkalmazás struktúrája megfelel-e annak, amit az App Service a [Django](#django-app) vagy a [Flask](#flask-app) számára elvár, vagy használjon [egyéni indítási parancsot](#customize-startup-command).

    - Ellenőrizze az [alkalmazás naplójának adatfolyamát](#access-diagnostic-logs) bármilyen hibaüzenet esetén. A naplók az alkalmazás kódjában előforduló hibákat jelenítik meg.

#### <a name="could-not-find-setuppy-or-requirementstxt"></a>Nem található setup.py vagy requirements.txt

- **A log stream a következőt jeleníti meg: "nem található Setup.py vagy requirements.txt; Nem fut a pip telepítése. "**: a Oryx-létrehozási folyamat nem találta a *requirements.txt* fájlt.

    - Csatlakozzon az [SSH](#open-ssh-session-in-browser) -n keresztül a Web App-tárolóhoz, és ellenőrizze, hogy a *requirements.txt* neve helyesen van-e, és létezik-e közvetlenül a *site/wwwroot* alatt. Ha nem létezik, tegye meg a fájlt a tárházban, és tartalmazza a központi telepítés részét képező helyet. Ha egy különálló mappában van, helyezze át a gyökerébe.

#### <a name="other-issues"></a>Egyéb problémák

- A jelszó **nem jelenik meg az SSH-munkamenetben, ha** a típus: biztonsági okokból az SSH-munkamenet megtartja a beírt jelszót. A karakterek rögzítése folyamatban van, ezért a szokásos módon írja be a jelszót, majd nyomja le az **ENTER** billentyűt a kész gombra.

- **Úgy tűnik, hogy az SSH-munkamenetben lévő parancsok le lesznek vágva**: Előfordulhat, hogy a szerkesztő nem lehet sortörési parancs, de a megfelelő futtatást is el kell végezni.

- A **statikus eszközök nem jelennek meg a Django alkalmazásban**: Győződjön meg arról, hogy engedélyezte a [whitenoise modult](http://whitenoise.evans.io/en/stable/django.html) .

- **A "végzetes SSL-kapcsolat szükséges" üzenet jelenik meg**: Ellenőrizze, hogy az alkalmazásból milyen felhasználóneveket és jelszavakat lehet elérni az erőforrásokhoz (például adatbázisokhoz).

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Python-alkalmazás és PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Oktatóanyag: üzembe helyezés Private Container adattárból](tutorial-custom-container.md?pivots=container-linux)

> [!div class="nextstepaction"]
> [App Service a Linuxon – gyakori kérdések](faq-app-service-linux.md)

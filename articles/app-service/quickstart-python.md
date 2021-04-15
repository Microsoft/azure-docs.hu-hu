---
title: 'Rövid útmutató: Python-alkalmazás létrehozása'
description: A Azure App Service első Python-alkalmazását üzembe helyezheti egy Linux-tárolóban a App Service.
ms.topic: quickstart
ms.date: 11/10/2020
ms.custom: seo-python-october2019, cli-validate, devx-track-python
zone_pivot_groups: python-frameworks-01
adobe-target: true
adobe-target-activity: DocsExp–393165–A/B–Docs/PythonQuickstart–CLIvsPortal–FY21Q4
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-python-portal
ms.openlocfilehash: d8af7a9744fe136f482fc007dbd0e51a680df932
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379485"
---
# <a name="quickstart-create-a-python-app-using-azure-app-service-on-linux"></a>Rövid útmutató: Python-alkalmazás létrehozása a Azure App Service on Linux

Ebben a rövid útmutatóban üzembe helyez [](overview.md#app-service-on-linux)egy Python-webalkalmazást a App Service on Linux, az Azure nagymértékben skálázható, önjavítást indító webes üzemeltetési szolgáltatásában. A helyi Azure parancssori felület [(CLI)](/cli/azure/install-azure-cli) használatával Egy Mac, Linux vagy Windows rendszerű számítógépen telepíthet egy mintát a Flask- vagy a Django-keretrendszer használatával. A konfigurált webalkalmazás egy alapszintű App Service, amely kis költséggel jár az Azure-előfizetésben.

## <a name="set-up-your-initial-environment"></a>A kezdeti környezet beállítása

1. Aktív előfizetéssel rendelkezik egy Azure-fiókkal. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
1. Telepítse a <a href="https://www.python.org/downloads/" target="_blank">Python 3.6-os vagy újabb verzióját.</a>
1. Telepítse az <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> 2.0.80-as vagy újabb verzióját, amellyel parancsokat futtathat bármely rendszerhéjban az Azure-erőforrások kiépítése és konfigurálása érdekében.

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

Ellenőrizze, hogy az Azure CLI 2.0.80-as vagy újabb verziója van-e:

```azurecli
az --version
```

Ezután jelentkezzen be az Azure-ba a CLI-ről:

```azurecli
az login
```

Ez a parancs megnyit egy böngészőt, amely összegyűjti a hitelesítő adatait. Amikor a parancs befejeződik, megjelenik az előfizetésekkel kapcsolatos információkat tartalmazó JSON-kimenet.

Miután bejelentkezett, Azure-parancsokat futtathat az Azure CLI-val az előfizetés erőforrásainak munkához.

Problémákat? [Tudajuk meg,](https://aka.ms/FlaskCLIQuickstartHelp)hogy .

## <a name="clone-the-sample"></a>A minta klónozása

Klónozza a mintaadattárat a következő paranccsal, és lépjen a mintamappába. ([Telepítse a gitet,](https://git-scm.com/downloads) ha még nem rendelkezik a git használatával.)

::: zone pivot="python-framework-flask"
```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-world
```
::: zone-end

::: zone pivot="python-framework-django"
```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-django
```
::: zone-end

A minta keretrendszer-specifikus kódot tartalmaz, Azure App Service az alkalmazás indítókor. További információ: [Tárolóindítási folyamat.](configure-language-python.md#container-startup-process)

Problémákat? [Tudajuk meg,](https://aka.ms/FlaskCLIQuickstartHelp)hogy .

## <a name="run-the-sample"></a>Minta futtatása

::: zone pivot="python-framework-flask"
1. Lépjen a következő mappába a *python-docs-hello-world mappában:*

    ```terminal
    cd python-docs-hello-world
    ```

1. Hozzon létre egy virtuális környezetet, és telepítse a függőségeket:

    [!include [virtual environment setup](../../includes/app-service-quickstart-python-venv.md)]

    Ha "[Errno 2] Nincs ilyen fájl vagy könyvtár: "requirements.txt", győződjön meg arról, hogy a *python-docs-hello-world mappában* van.

1. Futtassa a fejlesztői kiszolgálót.

    ```terminal  
    flask run
    ```
    
    Alapértelmezés szerint a kiszolgáló azt feltételezi, hogy az alkalmazás beviteli modulja a app.py *található,* ahogyan az a mintában is látható.

    Ha más modulnevet használ, állítsa a `FLASK_APP` környezeti változót erre a névre.

    Ha a következő hibaüzenet jelenik meg: "Nem található Flask-alkalmazás. Nem adott meg "FLASK_APP" környezeti változót, és a "wsgi.py" vagy "app.py" modul nem található az aktuális könyvtárban.", győződjön meg arról, hogy a mintát tartalmazó mappában `python-docs-hello-world` van.

1. Nyisson meg egy webböngészőt, és nyissa meg a mintaalkalmazást a következő webhelyen: `http://localhost:5000/` . Az alkalmazás a **Hello, World!**.

    ![Python-mintaalkalmazás helyi futtatása](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)
    
1. A terminálablakban nyomja le a Ctrl C **billentyűkombinációt** +  a fejlesztői kiszolgálóból való kilépéshez.
::: zone-end

::: zone pivot="python-framework-django"
1. Lépjen a *python-docs-hello-django mappába:*

    ```terminal
    cd python-docs-hello-django
    ```

1. Hozzon létre egy virtuális környezetet, és telepítse a függőségeket:

    [!include [virtual environment setup](../../includes/app-service-quickstart-python-venv.md)]

    Ha "[Errno 2] Nincs ilyen fájl vagy könyvtár: "requirements.txt", győződjön meg arról, hogy a *python-docs-hello-django mappában* van.
    
1. Futtassa a fejlesztői kiszolgálót.

    ```terminal
    python manage.py runserver
    ```

1. Nyisson meg egy webböngészőt, és nyissa meg a mintaalkalmazást a következő webhelyen: `http://localhost:8000/` . Az alkalmazás a **Hello, World!**. üzenetet jeleníti meg.

    ![Python-mintaalkalmazás helyi futtatása](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)
    
1. A terminálablakban nyomja le a **Ctrl** C + **billentyűkombinációt** a fejlesztői kiszolgálóból való kilépéshez.
::: zone-end

Problémákat? [Tudajuk meg,](https://aka.ms/FlaskCLIQuickstartHelp)hogy .

## <a name="deploy-the-sample"></a>A minta üzembe helyezése

Telepítse a kódot a helyi mappában *(python-docs-hello-world*) az `az webapp up` paranccsal:

```azurecli
az webapp up --sku B1 --name <app-name>
```

- Ha a parancs nem ismerhető fel, győződjön meg arról, hogy az Azure CLI telepítve van a `az` Kezdeti környezet beállítása szakaszban leírtak [szerint.](#set-up-your-initial-environment)
- Ha a parancs nem ismerhető fel, mert az `webapp` Azure CLI verziója 2.0.80-as vagy újabb. Ha nem, [telepítse a legújabb verziót.](/cli/azure/install-azure-cli)
- Cserélje le a helyére a teljes Azure-ban egyedi nevet ( érvényes `<app_name>` *karakterek: `a-z` , `0-9` és `-`*). Jó minta lehet a vállalat nevének és egy alkalmazásazonosítónak a kombinációja.
- A argumentum az Alapszintű tarifacsomagban hozza létre `--sku B1` a webalkalmazást, amely kis óránkénti költségeket okoz. Ezt az argumentumot kihagyva gyorsabb prémium szintet használjon.
- Igény szerint meg is használhatja a `--location <location-name>` argumentumot, ahol az egy elérhető `<location_name>` Azure-régió. Az Azure-fiókhoz elérhető régiók listáját az parancs futtatásával lehet [`az account list-locations`](/cli/azure/appservice#az-appservice-list-locations) lekérni.
- Ha a "Nem sikerült automatikusan észlelni az alkalmazás futtatókörnyezeti vermét" hibaüzenet jelenik meg, győződjön meg arról, hogy a parancsot a *python-docs-hello-world* mappában (Flask) vagy a *python-docs-hello-django* mappában (Django) futtatja, amely a *requirements.txt-fájlt* tartalmazza. [(Lásd: Az az webapp up](https://github.com/Azure/app-service-linux-docs/blob/master/AzWebAppUP/runtime_detection.md) (GitHub) automatikus észlelési hibáinak elhárítása.)

A parancs befejezése eltarthat néhány percig. Futtatás közben üzeneteket biztosít az erőforráscsoport létrehozásáról, a App Service tervezéséről és futtatásáról, a naplózás konfigurálásról, majd a ZIP üzembe helyezéséről. Ezután a következő üzenet jelenik meg: "Az alkalmazást http:// .azurewebsites.net alkalmazásnéven indíthatja el, amely az alkalmazás &lt; URL-címe az &gt; Azure-ban.

![Az az webapp up parancs példakimenete](./media/quickstart-python/az-webapp-up-output.png)

Problémákat? Először tekintse meg [a hibaelhárítási útmutatót,](configure-language-python.md#troubleshooting)egyéb esetben [pedig tudhatja velünk a következőt:](https://aka.ms/FlaskCLIQuickstartHelp).

[!include [az webapp up command note](../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Az alkalmazás megkeresése tallózással

Keresse meg az üzembe helyezett alkalmazást a webböngészőben a következő URL-címen: `http://<app-name>.azurewebsites.net` . Az alkalmazás elindulhat egy-két percet, ezért ha egy alapértelmezett alkalmazásoldalt lát, várjon egy percet, és frissítse a böngészőt.

A Python-mintakód egy Linux-tárolót futtat a App Service beépített rendszerkép használatával.

![Python-mintaalkalmazás futtatása az Azure-ban](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Gratulálunk!** Üzembe helyezett egy Python-alkalmazást a App Service.

Problémákat? Először tekintse meg [a hibaelhárítási útmutatót,](configure-language-python.md#troubleshooting)egyéb esetben [pedig tudhatja velünk a következőt:](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="redeploy-updates"></a>Frissítések ismételt üzembe telepítése

Ebben a szakaszban egy kisebb kódváltozást fog eltírni, majd újra üzembe kell majd üzembe edni a kódot az Azure-ban. A kód módosítása tartalmaz egy utasítást, amely naplókimenetet hoz `print` létre, amely a következő szakaszban fog dolgozni.

::: zone pivot="python-framework-flask"
Nyissa *app.py* egy szerkesztőben, és frissítse a függvényt az `hello` alábbi kódnak megfelelően. 

```python
def hello():
    print("Handling request to home page.")
    return "Hello, Azure!"
```
::: zone-end
::: zone pivot="python-framework-django"
Nyissa *meg a hello/views.py* fájlját egy szerkesztőben, és frissítse a függvényt úgy, hogy `hello` az megfeleljen az alábbi kódnak.

```python
def hello(request):
    print("Handling request to home page.")
    return HttpResponse("Hello, Azure!")
```
::: zone-end
    
Mentse a módosításokat, majd az paranccsal ismét üzembe kell `az webapp up` mentenie az alkalmazást:

```azurecli
az webapp up
```

Ez a parancs olyan értékeket használ, amelyek helyileg vannak gyorsítótárazva az *.azure/config* fájlban, beleértve az alkalmazás nevét, az erőforráscsoportot és a App Service tervet.

Az üzembe helyezés befejezése után váltson vissza a megnyitott `http://<app-name>.azurewebsites.net` böngészőablakra. Frissítse az oldalt, ahol megjelenik a módosított üzenet:

![Frissített Python-mintaalkalmazás futtatása az Azure-ban](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

Problémákat? Először tekintse meg [a hibaelhárítási útmutatót,](configure-language-python.md#troubleshooting)egyéb esetben [pedig tudhatja a következőt:](https://aka.ms/FlaskCLIQuickstartHelp).

> [!TIP]
> A Visual Studio Code hatékony bővítményeket biztosít a Pythonhoz és a Azure App Service-hoz, ami leegyszerűsíti a Python-webalkalmazások üzembe helyezését a App Service. További információ: [Python-alkalmazások](/azure/python/tutorial-deploy-app-service-on-linux-01)üzembe helyezése App Service a Visual Studio kódból.

## <a name="stream-logs"></a>Naplók streamelése

Az alkalmazáson belül létrehozott konzolnaplókat és a tárolót, amelyben fut, elérheti. A naplók tartalmazzák az utasítások használatával létrehozott `print` kimeneteket.

A naplók streamelésekor futtassa [az az webapp log tail](/cli/azure/webapp/log#az_webapp_log_tail) parancsot:

```azurecli
az webapp log tail
```

A paramétert az then paranccsal is meg lehet nyitni az üzembe `--logs` `az webapp up` helyezéskor a naplóstream automatikus megnyitásához.

Frissítse az alkalmazást a böngészőben a konzolnaplók létrehozásához, amelyek az alkalmazásnak küldött HTTP-kéréseket leíró üzeneteket tartalmaznak. Ha nem jelenik meg azonnal kimenet, próbálkozzon újra 30 másodpercen belül.

A naplófájlokat a böngészőből is megtekintheti a következő címen: `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

A naplóstreamelést bármikor leállíthatja a **terminálban** a + **Ctrl C** billentyűkombinációval.

Problémákat? Először tekintse meg [a hibaelhárítási útmutatót,](configure-language-python.md#troubleshooting)egyéb esetben [pedig tudhatja a következőt:](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="manage-the-azure-app"></a>Az Azure-alkalmazás kezelése

A létrehozott <a href="https://portal.azure.com" target="_blank">Azure Portal</a> az alkalmazás kezeléséhez. Keresse meg és válassza a **App Services.**

![Lépjen a App Services a Azure Portal](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

Válassza ki az Azure-alkalmazás nevét.

![Lépjen a Python-alkalmazásra a App Services a Azure Portal](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

Az alkalmazás kiválasztása megnyitja az **Áttekintés** lapot, ahol olyan alapvető felügyeleti feladatokat hajthat végre, mint a tallózás, leállítás, indítás, újraindítás és törlés.

![A Python-alkalmazás kezelése az áttekintési oldal Azure Portal](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

A App Service menü különböző oldalakat biztosít az alkalmazás konfigurálásához.

Problémákat? Először tekintse meg [a hibaelhárítási útmutatót,](configure-language-python.md#troubleshooting)egyéb esetben [pedig tudhatja velünk a következőt:](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az előző lépésekben Azure-erőforrásokat hozott létre egy erőforráscsoportban. Az erőforráscsoport neve helytől függően appsvc_rg_Linux_CentralUS "appsvc_rg_Linux_CentralUS" lehet. Ha továbbra is futtatja a webalkalmazást, további folyamatos költségekkel fog járni (lásd a App Service [díjszabását).](https://azure.microsoft.com/pricing/details/app-service/linux/)

Ha várhatóan nem lesz szüksége ezekre az erőforrásokra a jövőben, törölje az erőforráscsoportot a következő parancs futtatásával:

```azurecli
az group delete --no-wait
```

A parancs az *.azure/config fájlban gyorsítótárazott erőforráscsoport-nevet* használja.

A `--no-wait` argumentum lehetővé teszi, hogy a parancs a művelet befejezése előtt térjen vissza.

Problémákat? [Tudajuk meg,](https://aka.ms/FlaskCLIQuickstartHelp)hogy .

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Python- (Django-) webalkalmazás PostgreSQL-lel](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Python-alkalmazás konfigurálása](configure-language-python.md)

> [!div class="nextstepaction"]
> [Felhasználói bejelentkezés hozzáadása Python-webalkalmazáshoz](../active-directory/develop/quickstart-v2-python-webapp.md)

> [!div class="nextstepaction"]
> [Oktatóanyag: Python-alkalmazás futtatása egyéni tárolóban](tutorial-custom-container.md)

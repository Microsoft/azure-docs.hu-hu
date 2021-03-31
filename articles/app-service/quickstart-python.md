---
title: 'Gyors útmutató: Python-alkalmazás létrehozása'
description: Ismerkedjen meg Azure App Service az első Python-alkalmazás üzembe helyezésével a App Service Linux-tárolójában.
ms.topic: quickstart
ms.date: 11/10/2020
ms.custom: seo-python-october2019, cli-validate, devx-track-python, devx-track-azurecli
zone_pivot_groups: python-frameworks-01
adobe-target: true
adobe-target-activity: DocsExp–377467–A/B– Quickstarts/Python App–12.11
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-python-1
ms.openlocfilehash: 36c1b112127fadd5149c62a92ca469322db65edd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592353"
---
# <a name="quickstart-create-a-python-app-using-azure-app-service-on-linux"></a>Gyors útmutató: Python-alkalmazás létrehozása Azure App Service használatával Linuxon

Ebben a rövid útmutatóban egy Python-webalkalmazást helyez üzembe a [Linuxon](overview.md#app-service-on-linux), az Azure rugalmasan méretezhető, önjavító webes üzemeltetési szolgáltatásának app Service. Egy Mac, Linux vagy Windows rendszerű számítógépen a helyi [Azure parancssori felület (CLI)](/cli/azure/install-azure-cli) segítségével helyezheti üzembe a mintát a lombiktal vagy a Django keretrendszerek használatával. Az Ön által konfigurált webalkalmazás ingyenes App Service szintet használ, így a cikk során nem jár költségekkel.

> [!TIP]
> Ha inkább a Visual Studio Code-ot szeretné használni, kövesse a **[Visual Studio code app Service](/azure/developer/python/tutorial-deploy-app-service-on-linux-01)** rövid útmutatót.

## <a name="set-up-your-initial-environment"></a>A kezdeti környezet beállítása

1. Rendelkeznie kell aktív előfizetéssel rendelkező Azure-fiókkal. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
1. Telepítse a <a href="https://www.python.org/downloads/" target="_blank">Python 3,6-es vagy újabb</a>verzióját.
1. Telepítse az <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> -2.0.80 vagy újabb verzióját, amellyel az Azure-erőforrások kiépítéséhez és konfigurálásához bármilyen rendszerhéjban parancsokat futtathat.

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

Győződjön meg arról, hogy az Azure CLI verziója 2.0.80 vagy magasabb:

```azurecli
az --version
```

Ezután jelentkezzen be az Azure-ba a CLI használatával:

```azurecli
az login
```

Ez a parancs egy böngészőt nyit meg a hitelesítő adatok összegyűjtéséhez. A parancs befejeződése után az előfizetésekkel kapcsolatos információkat tartalmazó JSON-kimenet jelenik meg.

Miután bejelentkezett, futtathatja az Azure-parancsokat az Azure CLI-vel, hogy az előfizetésében lévő erőforrásokkal működjön.

Problémák léptek fel? [Tudassa velünk](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="clone-the-sample"></a>A minta klónozása

A következő parancs használatával klónozott a minta tárházat, és navigáljon a minta mappájába. ([Telepítse a git](https://git-scm.com/downloads) -t, ha még nem rendelkezik a git-vel.)

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

A minta olyan keretrendszer-specifikus kódot tartalmaz, amelyet az alkalmazás indításakor Azure App Service felismeri. További információ: [tároló indítási folyamata](configure-language-python.md#container-startup-process).

Problémák léptek fel? [Tudassa velünk](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="run-the-sample"></a>Minta futtatása

::: zone pivot="python-framework-flask"
1. Navigáljon a *Python-docs-Hello-World* mappába:

    ```terminal
    cd python-docs-hello-world
    ```

1. Virtuális környezet létrehozása és függőségek telepítése:

    [!include [virtual environment setup](../../includes/app-service-quickstart-python-venv.md)]

    Ha a (z) "[errno 2] nincs ilyen fájl vagy könyvtár:" requirements.txt ".", győződjön meg róla, hogy a *Python-docs-Hello-World* mappában van.

1. Futtassa a fejlesztői kiszolgálót.

    ```terminal  
    flask run
    ```
    
    Alapértelmezés szerint a kiszolgáló azt feltételezi, hogy az alkalmazás beléptetési modulja a *app.py* található, a mintában használt módon.

    Ha más nevű modult használ, állítsa a `FLASK_APP` környezeti változót erre a névre.

    Ha a következő hibaüzenet jelenik meg: "nem található a lombik-alkalmazás. Nem adta meg a "FLASK_APP" környezeti változót, és egy "wsgi.py" vagy "app.py" modul nem található az aktuális könyvtárban. "Ellenőrizze, hogy a `python-docs-hello-world` mintát tartalmazó mappában van-e.

1. Nyisson meg egy webböngészőt, és nyissa meg a minta alkalmazást a következő címen: `http://localhost:5000/` . Az alkalmazás megjeleníti a **Hello, World!** üzenetet.

    ![Egy minta Python-alkalmazás helyi futtatása](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)
    
1. A terminál ablakban a **CTRL** C billentyűkombináció lenyomásával +  lépjen ki a fejlesztői kiszolgálóról.
::: zone-end

::: zone pivot="python-framework-django"
1. Navigáljon a *Python-docs-Hello-Django* mappájába:

    ```terminal
    cd python-docs-hello-django
    ```

1. Virtuális környezet létrehozása és függőségek telepítése:

    [!include [virtual environment setup](../../includes/app-service-quickstart-python-venv.md)]

    Ha a (z) "[errno 2] nincs ilyen fájl vagy könyvtár:" requirements.txt ".", győződjön meg róla, hogy a *Python-docs-Hello-Django* mappában van.
    
1. Futtassa a fejlesztői kiszolgálót.

    ```terminal
    python manage.py runserver
    ```

1. Nyisson meg egy webböngészőt, és nyissa meg a minta alkalmazást a következő címen: `http://localhost:8000/` . Az alkalmazás megjeleníti a **Hello, World!** üzenetet.

    ![Egy minta Python-alkalmazás helyi futtatása](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)
    
1. A terminál ablakban a **CTRL** C billentyűkombináció lenyomásával +  lépjen ki a fejlesztői kiszolgálóról.
::: zone-end

Problémák léptek fel? [Tudassa velünk](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="deploy-the-sample"></a>A minta üzembe helyezése

Telepítse a kódot a helyi mappájába (*Python-docs-Hello-World*) a következő `az webapp up` parancs használatával:

```azurecli
az webapp up --sku B1 --name <app-name>
```

- Ha a `az` parancs nem ismerhető fel, győződjön meg arról, hogy az Azure CLI telepítve van a [kezdeti környezet beállítása](#set-up-your-initial-environment)című témakörben leírtak szerint.
- Ha a `webapp` parancs nem ismerhető fel, mert az Azure CLI verziója 2.0.80 vagy magasabb. Ha nem, [telepítse a legújabb verziót](/cli/azure/install-azure-cli).
- Cserélje le a `<app_name>` karaktert az összes Azure-beli egyedi névre (*érvényes karakterek: `a-z` , `0-9` és `-`*). Jó példa a vállalat nevének és az alkalmazás-azonosító kombinációjának használatára.
- Az `--sku B1` argumentum létrehozza a webalkalmazást az alapszintű díjszabási szinten, amely kis óradíjat eredményez. Hagyja ki ezt az argumentumot a gyorsabb prémium szint használatához.
- Igény szerint megadhatja az argumentumot `--location <location-name>` , ahol az `<location_name>` egy elérhető Azure-régió. Az Azure-fiók számára engedélyezett régiók listáját a parancs futtatásával kérheti le [`az account list-locations`](/cli/azure/appservice#az-appservice-list-locations) .
- Ha a következő hibaüzenet jelenik meg: "nem sikerült automatikusan észlelni az alkalmazás futásidejű veremét", győződjön meg arról, hogy a parancsot a *Python-docs-Hello-World* mappában (lombik) vagy a *Python-docs-Hello-Django* mappában (Django) futtatja, amely tartalmazza a *requirements.txt* fájlt. (Lásd: [az WebApp up (GitHub) szolgáltatással kapcsolatos automatikus észlelési hibák elhárítása](https://github.com/Azure/app-service-linux-docs/blob/master/AzWebAppUP/runtime_detection.md) .)

A parancs végrehajtása több percet is igénybe vehet. A futtatása közben üzeneteket biztosít az erőforráscsoport létrehozásához, a App Service terv és az üzemeltetési alkalmazáshoz, a naplózás konfigurálásához, majd a ZIP-telepítés elindításához. Ezután a következő üzenet jelenik meg: "az alkalmazás elindítható a http:// &lt; app-name &gt; . azurewebsites.net" néven, amely az alkalmazás URL-címe az Azure-ban.

![Példa az az WebApp up parancs kimenetére](./media/quickstart-python/az-webapp-up-output.png)

Problémák léptek fel? Először olvassa el a [hibaelhárítási útmutatót](configure-language-python.md#troubleshooting), ha nem, [tudassa velünk](https://aka.ms/FlaskCLIQuickstartHelp).

[!include [az webapp up command note](../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Az alkalmazás megkeresése tallózással

Tallózással keresse meg az üzembe helyezett alkalmazást a böngészőben az URL-címen `http://<app-name>.azurewebsites.net` . Az alkalmazás elindításához akár egy-két percet is igénybe vehet, így ha megjelenik egy alapértelmezett alkalmazás lap, várjon egy percet, és frissítse a böngészőt.

A Python-mintakód egy beépített rendszerképpel futtatja App Service Linux-tárolóját.

![Minta Python-alkalmazás futtatása az Azure-ban](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Gratulálunk!** A Python-alkalmazás üzembe helyezése App Service.

Problémák léptek fel? Először olvassa el a [hibaelhárítási útmutatót](configure-language-python.md#troubleshooting), ha nem, [tudassa velünk](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="redeploy-updates"></a>Frissítések újratelepítése

Ebben a szakaszban egy kis kód módosítását hajtja végre, majd újból üzembe helyezi a kódot az Azure-ban. A kód módosítása tartalmaz egy `print` utasítást a következő szakaszban használt naplózási kimenet létrehozásához.

::: zone pivot="python-framework-flask"
Nyissa meg a *app.py* egy szerkesztőben, és frissítse a `hello` függvényt, hogy az megfeleljen a következő kódnak. 

```python
def hello():
    print("Handling request to home page.")
    return "Hello, Azure!"
```
::: zone-end
::: zone pivot="python-framework-django"
Nyissa meg a *Hello/views.* és a szerkesztőt, és frissítse a `hello` függvényt, hogy az megfeleljen a következő kódnak.

```python
def hello(request):
    print("Handling request to home page.")
    return HttpResponse("Hello, Azure!")
```
::: zone-end
    
Mentse a módosításokat, majd telepítse újra az alkalmazást a `az webapp up` parancs használatával:

```azurecli
az webapp up
```

Ez a parancs azokat az értékeket használja, amelyek helyileg vannak gyorsítótárazva a *. Azure/config* fájlban, beleértve az alkalmazás nevét, az erőforráscsoportot és a app Service tervet.

Az üzembe helyezés befejezése után váltson vissza a böngészőablakra, és nyissa meg a párbeszédpanelt `http://<app-name>.azurewebsites.net` . Frissítse az oldalt, amelynek a módosított üzenetnek kell megjelennie:

![Frissített minta Python-alkalmazás futtatása az Azure-ban](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

Problémák léptek fel? Először olvassa el a [hibaelhárítási útmutatót](configure-language-python.md#troubleshooting), ha nem, [tudassa velünk](https://aka.ms/FlaskCLIQuickstartHelp).

> [!TIP]
> A Visual Studio Code hatékony bővítményeket biztosít a Python és a Azure App Service számára, ami leegyszerűsíti a Python-webalkalmazások App Serviceba való üzembe helyezésének folyamatát. További információ: [Python-alkalmazások üzembe helyezése a Visual Studio Code-ból App Service](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="stream-logs"></a>Naplók streamelése

Elérheti az alkalmazáson belül létrehozott konzol naplóit, valamint azt a tárolót, amelyben a fut. A naplók tartalmazzák az utasítások használatával generált kimeneteket `print` .

A naplók továbbításához futtassa az az [WebApp log tail](/cli/azure/webapp/log#az_webapp_log_tail) parancsot:

```azurecli
az webapp log tail
```

Azt is megteheti, `--logs` hogy a paraméterrel együtt `az webapp up` automatikusan megnyitja a naplózási adatfolyamot a telepítéskor.

Frissítse az alkalmazást a böngészőben a konzol naplófájljainak létrehozásához, amelyek az alkalmazásnak küldött HTTP-kérelmeket leíró üzeneteket tartalmaznak. Ha nem jelenik meg azonnal a kimenet, próbálkozzon újra 30 másodperc múlva.

A naplófájlokat a böngészőből is megtekintheti a következő címen: `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Ha bármikor le szeretné állítani a naplózási adatfolyamot, nyomja le a **CTRL** + **C** billentyűkombinációt a terminálon.

Problémák léptek fel? Először olvassa el a [hibaelhárítási útmutatót](configure-language-python.md#troubleshooting), ha nem, [tudassa velünk](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="manage-the-azure-app"></a>Az Azure-alkalmazás kezelése

A létrehozott alkalmazás kezeléséhez lépjen a <a href="https://portal.azure.com" target="_blank">Azure Portal</a> . Keresse meg és válassza ki a **app Services**.

![Navigáljon App Services a Azure Portal](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

Válassza ki az Azure-alkalmazás nevét.

![Navigáljon a Python-alkalmazáshoz App Services a Azure Portal](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

Az alkalmazás kiválasztása megnyitja az **Áttekintés** lapot, ahol olyan alapszintű felügyeleti feladatokat hajthat végre, mint a Tallózás, Leállítás, indítás, újraindítás és törlés.

![A Python-alkalmazás kezelése a Azure Portal Áttekintés oldalán](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

A App Service menü különböző lapokat biztosít az alkalmazás konfigurálásához.

Problémák léptek fel? Először olvassa el a [hibaelhárítási útmutatót](configure-language-python.md#troubleshooting), ha nem, [tudassa velünk](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az előző lépésekben Azure-erőforrásokat hozott létre egy erőforráscsoportban. Az erőforráscsoport neve például "appsvc_rg_Linux_CentralUS", a tartózkodási helytől függően. Ha megtartja a webalkalmazást, további költségek merülnek fel (lásd: [app Service díjszabása](https://azure.microsoft.com/pricing/details/app-service/linux/)).

Ha nem várható, hogy a jövőben szüksége lesz ezekre az erőforrásokra, törölje az erőforráscsoportot a következő parancs futtatásával:

```azurecli
az group delete --no-wait
```

A parancs az *. Azure/config* fájlban gyorsítótárazott erőforráscsoport-nevet használja.

Az `--no-wait` argumentum lehetővé teszi, hogy a parancs visszaadja a műveletet a művelet befejeződése előtt.

Problémák léptek fel? [Tudassa velünk](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Python (Django) webalkalmazás és PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Python-alkalmazás konfigurálása](configure-language-python.md)

> [!div class="nextstepaction"]
> [Felhasználói bejelentkezés hozzáadása egy Python-webalkalmazáshoz](../active-directory/develop/quickstart-v2-python-webapp.md)

> [!div class="nextstepaction"]
> [Oktatóanyag: Python-alkalmazás futtatása egyéni tárolóban](tutorial-custom-container.md)

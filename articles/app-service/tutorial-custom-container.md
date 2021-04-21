---
title: 'Oktatóanyag: Egyéni rendszerkép létrehozása és futtatása a Azure App Service'
description: Részletes útmutató egyéni Linux- vagy Windows-rendszerkép létrehozásához, a rendszerkép Azure Container Registry- és üzembe helyezéséhez a Azure App Service. Megtudhatja, hogyan telepíthet át egyéni szoftvereket App Service egyéni tárolókba.
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: msangapu
keywords: azure app service, webalkalmazás, linux, windows, docker, tároló
ms.custom: devx-track-csharp, mvc, seodec18, devx-track-python, devx-track-azurecli
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 0770b46a60f497d3a3da772e7be13ece0526eca0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765700"
---
# <a name="migrate-custom-software-to-azure-app-service-using-a-custom-container"></a>Egyéni szoftver áttelepítése Azure App Service egyéni tároló használatával

::: zone pivot="container-windows"  

Az [Azure App Service](overview.md) előre meghatározott, IIS-en futó alkalmazáscsoportokat biztosít Windows rendszeren, például az ASP.NET-et vagy a Node.js-t. Az előre konfigurált Windows-környezet letiltja az operációs rendszeren például a rendszergazdai hozzáférést, a szoftvertelepítést vagy a globális szerelvény-gyorsítótár módosítását (lásd [az operációs rendszer Azure App Service-funkcionalitásával foglalkozó részt](operating-system-functionality.md)). Egy egyéni Windows-tároló az App Service-ben való használata lehetővé teszi az alkalmazáshoz szükséges operációs rendszer-módosítások végrehajtását, így egyszerűen áttelepítheti az egyéni operációsrendszer- és szoftverkonfigurációkat igénylő helyszíni alkalmazásokat. Ez az oktatóanyag bemutatja egy Windows betűkészlet-könyvtárba telepített egyéni betűkészletet használó ASP.NET-alkalmazás az App Service-be való áttelepítésének menetét. Egy Visual Studióból származó egyénileg konfigurált Windows-rendszerképet kell üzembe helyeznie az [Azure Container Registryben](../container-registry/index.yml), majd futtatnia az App Service-ben.

![Megjeleníti a Windows-tárolóban futó webalkalmazást.](media/tutorial-custom-container/app-running.png)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

- <a href="https://hub.docker.com/" target="_blank">Regisztráció Docker Hub-fiókra</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">Windows rendszerhez készült Docker telepítése</a>.
- <a href="/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">A Docker átváltása Windows-tárolók futtatására</a>.
- <a href="https://www.visualstudio.com/downloads/" target="_blank">Telepítse Visual Studio 2019-es</a> ASP.NET a webes **fejlesztéssel** és **az Azure-fejlesztési** számítási feladatokkal. Ha már telepítette Visual Studio 2019-es Visual Studio:
    - Telepítse a legújabb frissítéseket a Visual Studio a **Súgó frissítések**  >  **keresése parancsra kattintva.**
    - Adja hozzá a számítási feladatokat a Visual Studio **eszközök Eszközök** és  >  **szolgáltatások lekérte elemre kattintva.**

## <a name="set-up-the-app-locally"></a>Az alkalmazás helyi beállítása

### <a name="download-the-sample"></a>A minta letöltése

Ebben a lépésben a helyi .NET-projektet állíthatja be.

- [Töltse le a mintaprojektet](https://github.com/Azure-Samples/custom-font-win-container/archive/master.zip).
- Bontsa ki az Extract paranccsal a *custom-font-win-container.zip* fájlt.

A mintaprojekt tartalmaz egy egyszerű ASP.NET-alkalmazást, amely egy Windows betűkészlet-könyvtárba telepített egyéni betűkészletet használ. A betűkészletek telepítése nem szükséges, de láthat egy példát egy mögöttes operációs rendszerrel integrált alkalmazásra. Az ilyen alkalmazások App Service-be történő áttelepítéséhez vagy újraírja a kódját az integráció eltávolításához, vagy áttelepíti egy egyéni Windows-tárolóban a jelenlegi formájában.

### <a name="install-the-font"></a>Betűkészlet telepítése

A Windows Explorerben navigáljon a _custom-font-win-container-master/CustomFontSample_ elemhez, majd kattintson a jobb gombbal a _FrederickatheGreat-Regular.ttf_ fájlra, és válassza az **Install** (Telepítés) lehetőséget.

Ez a betűkészlet nyilvánosan elérhető innen: [Google Fonts](https://fonts.google.com/specimen/Fredericka+the+Great).

### <a name="run-the-app"></a>Az alkalmazás futtatása

Nyissa meg a *custom-font-win-container/CustomFontSample.sln* fájlt a Visual Studióban. 

Az alkalmazás hibakeresés nélküli futtatásához nyomja le a `Ctrl+F5` billentyűkombinációt. Az alkalmazás megjelenik az alapértelmezett böngészőben. 

:::image type="content" source="media/tutorial-custom-container/local-app-in-browser.png" alt-text="Képernyőkép az alkalmazás alapértelmezett böngészőben való megjelenítéséről.":::

Az alkalmazás nem futtatható az App Service tesztkörnyezetben, mert egy telepített betűkészletet használ. Egy Windows-tároló használatával viszont üzembe helyezheti, mert a betűkészlet a Windows-tárolóban telepíthető.

### <a name="configure-windows-container"></a>Windows-tároló konfigurálása

A Megoldáskezelőben kattintson jobb gombbal a **CustomFontSample** projektre, és válassza az **Add** > **Container Orchestration Support** (Hozzáadás > Container Orchestration Support) lehetőséget.

:::image type="content" source="media/tutorial-custom-container/enable-container-orchestration.png" alt-text="Képernyőkép a Megoldáskezelő, melyen a CustomFontSample projekt, a Hozzáadás és a Tárolóvezénylés támogatása menüpont van kiválasztva.":::

Válassza a **Docker Compose**  >  **OK lehetőséget.**

A projekt mostantól futtatható egy Windows-tárolóban. Egy _Docker-fájl_ lesz hozzáadva a **CustomFontSample** projekthez, illetve egy **docker-compose** projekt a megoldáshoz. 

Nyissa meg a **Docker-fájlt** a Megoldáskezelőből.

Egy [támogatott szülőrendszerképet](configure-custom-container.md#supported-parent-images) kell használnia. Módosítsa a szülőrendszerképet a `FROM` sor a következő kódra való lecserélésével:

```dockerfile
FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019
```

A fájl végéhez adja hozzá a következő sort, majd mentse a fájlt:

```dockerfile
RUN ${source:-obj/Docker/publish/InstallFont.ps1}
```

Az _InstallFont.ps1_ megtalálható a **CustomFontSample** projektben. Ez egy egyszerű szkript a betűkészlet telepítéséhez. A [Script Centerben](https://gallery.technet.microsoft.com/scriptcenter/fb742f92-e594-4d0c-8b79-27564c575133) megtalálja a szkript egy összetettebb verzióját.

> [!NOTE]
> A Windows-tároló helyi teszteléséhez győződjön meg arról, hogy a Docker elindult a helyi gépen.
>

## <a name="publish-to-azure-container-registry"></a>Közzététel az Azure Container Registryben

Az [Azure Container Registry](../container-registry/index.yml) képes tárolni a rendszerképeket a tárolók üzembe helyezéséhez. Az App Service-t az Azure Container Registryben üzemeltetett rendszerképek használatára konfigurálhatja.

### <a name="open-publish-wizard"></a>Közzétételi varázsló megnyitása

A Megoldáskezelőben kattintson jobb gombbal a **CustomFontSample** projektre, majd válassza a **Publish** (Közzététel) lehetőséget.

:::image type="content" source="media/tutorial-custom-container/open-publish-wizard.png" alt-text="Képernyőkép a Megoldáskezelő CustomFontSample projekt és a Kijelölt Közzététel elemről.":::

### <a name="create-registry-and-publish"></a>Beállításjegyzék létrehozása és közzététele

A közzétételi varázslóban válassza az **Új Container Registry**  >  **létrehozása Azure Container Registry**  >  **lehetőséget.**

:::image type="content" source="media/tutorial-custom-container/create-registry.png" alt-text="Képernyőkép a közzétételi varázslóról, amely Container Registry, az Új Azure Container Registry létrehozása és a kijelölt Közzététel gombot mutatja.":::

### <a name="sign-in-with-azure-account"></a>Bejelentkezés az Azure-fiók használatával

A **Create a new Azure Container Registry** (Új Azure Container Registry létrehozása) párbeszédpanelen válassza az **Add an account** (Fiók hozzáadása) gombot, majd jelentkezzen be az Azure-előfizetésébe. Ha már bejelentkezett, válassza ki a kívánt előfizetést tartalmazó fiókot a legördülő listából.

![Bejelentkezés az Azure-ba](./media/tutorial-custom-container/add-an-account.png)

### <a name="configure-the-registry"></a>A beállításjegyzék konfigurálása

Konfigurálja az új Container Registryt a következő táblázatban javasolt értékek alapján. Ha végzett, kattintson a **Létrehozás** gombra.

| Beállítás  | Ajánlott érték | További tudnivalók |
| ----------------- | ------------ | ----|
|**DNS-előtag**| Megtarthatja a beállításjegyzék létrehozott nevét, vagy módosíthatja egy másik egyedi névre. |  |
|**Erőforráscsoport**| Kattintson a **New** (Új) lehetőségre, írja be a **myResourceGroup** kifejezést, majd kattintson az **OK** gombra. |  |
|**Termékváltozat**| Alapszintű | [Árképzési szintek](https://azure.microsoft.com/pricing/details/container-registry/)|
|**Beállításjegyzékbeli hely**| Nyugat-Európa | |

![Az Azure Container Registry konfigurálása](./media/tutorial-custom-container/configure-registry.png)

Megnyílik egy terminálablak, és megjeleníti a rendszerkép üzembe helyezésének folyamatát. Várjon, amíg az üzembe helyezés befejeződik.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-web-app"></a>Webalkalmazás létrehozása

A bal oldali menüben válassza az **Erőforrás létrehozása**  >  **Webalkalmazás**  >  **Web App for Containers.**

### <a name="configure-app-basics"></a>Az alkalmazás alapjainak konfigurálása

Az Alapvető **beállítások lapon** konfigurálja a beállításokat a következő táblázat szerint, majd kattintson a **Tovább: Docker elemre.**

| Beállítás  | Ajánlott érték | További tudnivalók |
| ----------------- | ------------ | ----|
|**Előfizetés**| Győződjön meg arról, hogy a megfelelő előfizetés van kiválasztva. |  |
|**Erőforráscsoport**| Válassza **az Új létrehozása lehetőséget,** írja be a **myResourceGroup parancsot,** majd kattintson az **OK gombra.** |  |
|**Név**| Írjon be egy egyedi nevet. | A webalkalmazás URL-címe `http://<app-name>.azurewebsites.net`, amelyben az `<app-name>` az alkalmazás neve. |
|**Közzététel**| Docker-tároló | |
|**Operációs rendszer**| Windows | |
|**Régió**| Nyugat-Európa | |
|**Windows-csomag**| Válassza **az Új létrehozása lehetőséget,** írja be a **myAppServicePlan parancsot,** majd kattintson az **OK gombra.** | |

Az **Alapvető beállítások lapnak** így kell kinéznie:

![Megjeleníti a webalkalmazás konfiguráláshoz használt Alapvető beállítások lapot.](media/tutorial-custom-container/configure-app-basics.png)

### <a name="configure-windows-container"></a>Windows-tároló konfigurálása

A **Docker lapon** konfigurálja az egyéni Windows-tárolót az alábbi táblázatban látható módon, majd válassza a **Felülvizsgálat + létrehozás lehetőséget.**

| Beállítás  | Ajánlott érték |
| ----------------- | ------------ |
|**Kép forrása**| Azure Container Register |
|**Registry**| Válassza [ki a korábban létrehozott beállításjegyzéket.](#publish-to-azure-container-registry) |
|**Kép**| customfontsample (egyénifontsample) |
|**Tag**| legújabb |

### <a name="complete-app-creation"></a>Alkalmazás létrehozásának befejezése

Kattintson a **Létrehozás** parancsra, majd várjon, amíg az Azure létrehozza a szükséges erőforrásokat.

## <a name="browse-to-the-web-app"></a>A webalkalmazás kikeresése

Értesítés jelenik meg, ha az Azure befejezte a művelet végrehajtását.

![Azt mutatja, hogy az Azure-művelet befejeződött.](media/tutorial-custom-container/portal-create-finished.png)

1. Kattintson az **Erőforrás megnyitása** lehetőségre.

2. Az alkalmazás oldalán kattintson az **URL-cím** mező alatti hivatkozásra.

Egy új böngészőlapon a következő oldal jelenik meg:

![Megjeleníti a webalkalmazás új böngészőoldalát.](media/tutorial-custom-container/app-starting.png)

Várjon néhány percet, és próbálkozzon újra, amíg meg nem jelenik a kezdőlap a várt, gyönyörű betűtípussal:

![Megjeleníti a kezdőlapot a beállított betűtípussal.](media/tutorial-custom-container/app-running.png)

**Gratulálunk!** Áttelepített egy ASP.NET-alkalmazást az Azure App Service szolgáltatásba egy Windows-tárolóban.

## <a name="see-container-start-up-logs"></a>A tároló rendszerindítási naplóinak megtekintése

A Windows-tároló betöltése hosszabb időbe telhet. A folyamat előrehaladásának nyomon lépéséhez lépjen a következő URL-címre úgy, hogy a et az *\<app-name>* alkalmazás nevére cseréli.
```
https://<app-name>.scm.azurewebsites.net/api/logstream
```

A streamelt naplók a következőképpen néznek ki:

```
14/09/2018 23:16:19.889 INFO - Site: fonts-win-container - Creating container for image: customfontsample20180914115836.azurecr.io/customfontsample:latest.
14/09/2018 23:16:19.928 INFO - Site: fonts-win-container - Create container for image: customfontsample20180914115836.azurecr.io/customfontsample:latest succeeded. Container Id 329ecfedbe370f1d99857da7352a7633366b878607994ff1334461e44e6f5418
14/09/2018 23:17:23.405 INFO - Site: fonts-win-container - Start container succeeded. Container: 329ecfedbe370f1d99857da7352a7633366b878607994ff1334461e44e6f5418
14/09/2018 23:17:28.637 INFO - Site: fonts-win-container - Container ready
14/09/2018 23:17:28.637 INFO - Site: fonts-win-container - Configuring container
14/09/2018 23:18:03.823 INFO - Site: fonts-win-container - Container ready
14/09/2018 23:18:03.823 INFO - Site: fonts-win-container - Container start-up and configuration completed successfully
```

::: zone-end

::: zone pivot="container-linux"

Azure App Service a Docker tárolótechnológiáját használja a beépített rendszerképek és az egyéni rendszerképek számára is. A beépített rendszerképek listájának listához futtassa az ["az webapp list-runtimes --linux" Azure CLI-parancsot.](/cli/azure/webapp#az_webapp_list_runtimes) Ha ezek a rendszerképek nem tesznek eleget az igényeinek, létrehozhat és üzembe helyezhet egy egyéni rendszerképet.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Egyéni rendszerkép létrehozása, ha nincs az igényeinek megfelelő beépített rendszerkép
> * Az egyéni rendszerkép leküldése egy privát tároló-beállításjegyzékbe az Azure-ban
> * Futtassa az egyéni rendszerképet a App Service
> * Környezeti változók konfigurálása
> * A rendszerkép frissítése és ismételt üzembeása
> * Diagnosztikai naplók elérése
> * Csatlakozás a tárolóhoz SSH használatával

Ennek az oktatóanyagnak az elvégzése kis költséget jelent az Azure-fiókjában a tároló-beállításjegyzékért, és további költségekkel jár a tároló egy hónapnál hosszabb ideig tartó üzemeltetése.

## <a name="set-up-your-initial-environment"></a>A kezdeti környezet beállítása

- Aktív előfizetéssel rendelkezik egy Azure-fiókkal. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Telepítse a [Docker](https://docs.docker.com/get-started/#setup)alkalmazást, amellyel Docker-rendszerképeket lehet összeépíteni. A Docker telepítéséhez szükség lehet a számítógép újraindítására.
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
- Ehhez az oktatóanyaghoz az Azure CLI 2.0.80-as vagy újabb verziójára lesz szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

A Docker telepítése vagy a Azure Cloud Shell után nyisson meg egy terminálablakot, és ellenőrizze, hogy a Docker telepítve van-e:

```bash
docker --version
```

## <a name="clone-or-download-the-sample-app"></a>A mintaalkalmazás klónozása vagy letöltése

Az oktatóanyaghoz szükséges mintát git-klónon vagy letöltésen keresztül szerezheti be.

### <a name="clone-with-git"></a>Klónozás Git használatával

Klónozza a mintaadattárat:

```terminal
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
```

A Linux-tárolóban használt fájlokban a megfelelő sorvégződések garantálása érdekében mindenképpen használja a `--config core.autocrlf=input` argumentumot:

Ezután a következő mappába kell ásni:

```terminal
cd docker-django-webapp-linux
```

### <a name="download-from-github"></a>Letöltés a GitHubról

Git-klón használata helyett látogasson el a webhelyre, válassza a [https://github.com/Azure-Samples/docker-django-webapp-linux](https://github.com/Azure-Samples/docker-django-webapp-linux) **Clone**(Klónozás) lehetőséget, majd a Download ZIP **(ZIP letöltése) lehetőséget.** 

Csomagolja ki a ZIP-fájlt egy *docker-django-webapp-linux nevű mappába.* 

Ezután nyisson meg egy terminálablakot a *docker-django-webapp-linux mappában.*

## <a name="optional-examine-the-docker-file"></a>(Nem kötelező) A Docker-fájl vizsgálata

A _Dockerfile_ nevű mintafájl, amely leírja a Docker-rendszerképet, és tartalmazza a konfigurációs utasításokat:

```Dockerfile
FROM tiangolo/uwsgi-nginx-flask:python3.6

RUN mkdir /code
WORKDIR /code
ADD requirements.txt /code/
RUN pip install -r requirements.txt --no-cache-dir
ADD . /code/

# ssh
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "$SSH_PASSWD" | chpasswd 

COPY sshd_config /etc/ssh/
COPY init.sh /usr/local/bin/
    
RUN chmod u+x /usr/local/bin/init.sh
EXPOSE 8000 2222

#CMD ["python", "/code/manage.py", "runserver", "0.0.0.0:8000"]
ENTRYPOINT ["init.sh"]
```

* Az első parancscsoport telepíti az alkalmazás követelményeit a környezetben.
* A második parancscsoport egy [SSH-kiszolgálót](https://www.ssh.com/ssh/protocol/) hoz létre a tároló és a gazdagép közötti biztonságos kommunikációhoz.
* Az utolsó sor `ENTRYPOINT ["init.sh"]` () az `init.sh` meghívását hívja meg az SSH-szolgáltatás és a Python-kiszolgáló indításhoz.

## <a name="build-and-test-the-image-locally"></a>A rendszerkép helyi összeállítása és tesztelése

> [!NOTE]
> Docker Hub IP-címenkénti névtelen lekért adatok és az ingyenes felhasználónkénti hitelesített le pull-címek száma kvótával rendelkezik [(lásd: **Adatátvitel**)](https://www.docker.com/pricing). Ha azt veszi észre, hogy a Docker Hub lekért adatok korlátozottak, próbálja meg, ha még nincs `docker login` bejelentkezve.
> 

1. Futtassa a következő parancsot a rendszerkép felépítéséhez:

    ```bash
    docker build --tag appsvc-tutorial-custom-image .
    ```
    
1. Tesztelje a build működését a Docker-tároló helyi futtatásával:

    ```bash
    docker run -p 8000:8000 appsvc-tutorial-custom-image
    ```
    
    Ez a parancs a portot a argumentummal és a lemezkép [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) `-p` nevével adja meg. 
    
    > [!TIP]
    > Ha Windows rendszeren fut, és a következő hibaüzenetet látja: *standard_init_linux.go:211: exec user process caused "no such file or directory"*(nincs ilyen fájl vagy könyvtár) a *init.sh-fájl* CR-LF sorvégződéseket tartalmaz a várt LF végződés helyett. Ez a hiba akkor fordul elő, ha a git használatával klónozta a mintaadattárat, de kihagyta a `--config core.autocrlf=input` paramétert. Ebben az esetben klónozza újra az adattárat a --config argumentummal. A hiba akkor is előfordulhat,  ha szerkesztette a init.sh CRLF végződésűre mentette. Ebben az esetben mentse újra a fájlt csak LF végződésekkel.

1. Tallózással ellenőrizze, hogy a webalkalmazás és a tároló `http://localhost:8000` megfelelően működik-e.

    ![Webalkalmazás helyi tesztelése](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Ebben és a következő szakaszban kiépíti azokat az erőforrásokat az Azure-ban, amelyekbe lekultálja a rendszerképet, majd üzembe helyez egy tárolót a Azure App Service. Először létrehoz egy erőforráscsoportot, amelyben összegyűjti ezeket az erőforrásokat.

Futtassa [az az group create parancsot](/cli/azure/group#az_group_create) egy erőforráscsoport létrehozásához:

```azurecli-interactive
az group create --name AppSvc-DockerTutorial-rg --location westus2
```

Módosíthatja az `--location` értéket, hogy egy közeli régiót adjon meg.

## <a name="push-the-image-to-azure-container-registry"></a>A rendszerkép leküldése Azure Container Registry

Ebben a szakaszban lekulpeli a rendszerképet Azure Container Registry, App Service üzembe tudja helyezni.

1. Futtassa [`az acr create`](/cli/azure/acr#az_acr_create) az parancsot egy új Azure Container Registry:

    ```azurecli-interactive
    az acr create --name <registry-name> --resource-group AppSvc-DockerTutorial-rg --sku Basic --admin-enabled true
    ```
    
    Cserélje `<registry-name>` le a helyére a beállításjegyzék megfelelő nevét. A név csak betűket és számokat tartalmazhat, és egyedinek kell lennie az Azure-ban.

1. Futtassa [`az acr show`](/cli/azure/acr#az_acr_show) az parancsot a beállításjegyzék hitelesítő adatainak lekéréséhez:

    ```azurecli-interactive
    az acr credential show --resource-group AppSvc-DockerTutorial-rg --name <registry-name>
    ```
    
    A parancs JSON-kimenete két jelszót és a beállításjegyzék felhasználónevét biztosítja.
    
1. Az `docker login` paranccsal jelentkezzen be a tároló-beállításjegyzékbe:

    ```bash
    docker login <registry-name>.azurecr.io --username <registry-username>
    ```
    
    Cserélje le `<registry-name>` a és `<registry-username>` a értékeket az előző lépésekből származó értékekre. Amikor a rendszer kéri, írja be az előző lépésben használt jelszavak valamelyikét.

    A szakasz további lépéseinél ugyanazt a beállításjegyzék-nevet használja.

1. Ha a bejelentkezés sikeres, címkézd fel a helyi Docker-rendszerképet a regisztrációs adatbázishoz:

    ```bash
   docker tag appsvc-tutorial-custom-image <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```    

1. A `docker push` paranccsal lekérte a rendszerképet a beállításjegyzékbe:

    ```bash
    docker push <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

    A rendszerkép első feltöltése eltarthat néhány percig, mert tartalmazza az alapként felhozott rendszerképet. A későbbi feltöltések általában gyorsabbak.

    Amíg várakozik, a következő szakaszban található lépésekkel konfigurálhatja a regisztrációs adatbázisból App Service üzembe helyezést.

1. A `az acr repository list` paranccsal ellenőrizze, hogy a leküldés sikeres volt-e:

    ```azurecli-interactive
    az acr repository list -n <registry-name>
    ```
    
    A kimenetnek meg kell mutatnia a rendszerkép nevét.


## <a name="configure-app-service-to-deploy-the-image-from-the-registry"></a>A App Service konfigurálása a rendszerkép beállításjegyzékből való üzembe helyezéséhez

Egy tároló üzembe helyezéséhez Azure App Service létre egy webalkalmazást a App Service, majd csatlakoztassa a webalkalmazást a tárolójegyzékhez. A webalkalmazás indításakor a App Service automatikusan lekérte a rendszerképet a beállításjegyzékből.

1. Hozzon létre egy App Service tervet az [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) paranccsal:

    ```azurecli-interactive
    az appservice plan create --name AppSvc-DockerTutorial-plan --resource-group AppSvc-DockerTutorial-rg --is-linux
    ```

    A App Service csomag megfelel a webalkalmazást tartalmazó virtuális gépnek. Alapértelmezés szerint az előző parancs egy olcsó [B1 tarifacsomagot használ,](https://azure.microsoft.com/pricing/details/app-service/linux/) amely az első hónapban ingyenes. A szintet a paraméterrel `--sku` szabályozhatja.

1. Hozza létre a webalkalmazást az [`az webpp create`](/cli/azure/webapp#az_webapp_create) paranccsal:

    ```azurecli-interactive
    az webapp create --resource-group AppSvc-DockerTutorial-rg --plan AppSvc-DockerTutorial-plan --name <app-name> --deployment-container-image-name <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```
    
    Cserélje `<app-name>` le a helyére a webalkalmazás nevét, amelynek az Egész Azure-ban egyedinek kell lennie. Cserélje le `<registry-name>` a helyére az előző szakaszból származó regisztrációs adatbázis nevét is.

1. A [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) használatával állítsa be a környezeti `WEBSITES_PORT` változót az alkalmazáskód által várt módon: 

    ```azurecli-interactive
    az webapp config appsettings set --resource-group AppSvc-DockerTutorial-rg --name <app-name> --settings WEBSITES_PORT=8000
    ```

    Cserélje `<app-name>` le a helyére az előző lépésben használt nevet.
    
    A környezeti változóval kapcsolatos további információkért tekintse meg a minta [GitHub-adattárának információs fájlját.](https://github.com/Azure-Samples/docker-django-webapp-linux)

1. Engedélyezze a felügyelt [identitást](./overview-managed-identity.md) a webalkalmazáshoz az [`az webapp identity assign`](/cli/azure/webapp/identity#az_webapp_identity-assign) paranccsal:

    ```azurecli-interactive
    az webapp identity assign --resource-group AppSvc-DockerTutorial-rg --name <app-name> --query principalId --output tsv
    ```

    Cserélje `<app-name>` le a helyére az előző lépésben használt nevet. A parancs kimenete (a és argumentumokkal szűrve) a hozzárendelt identitás szolgáltatásnévje, amelyet hamarosan `--query` `--output` használni fog.

    A felügyelt identitással konkrét hitelesítő adatok nélkül adhat engedélyeket a webalkalmazásnak más Azure-erőforrások eléréséhez.

1. Az előfizetés-azonosítót az paranccsal kell lekérni, amelyre a [`az account show`](/cli/azure/account#az_account_show) következő lépésben lesz szüksége:

    ```azurecli-interactive
    az account show --query id --output tsv
    ``` 

1. Adjon engedélyt a webalkalmazásnak a tároló-beállításjegyzék elérésére:

    ```azurecli-interactive
    az role assignment create --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourceGroups/AppSvc-DockerTutorial-rg/providers/Microsoft.ContainerRegistry/registries/<registry-name> --role "AcrPull"
    ```

    Cserélje le a következő értékeket:
    - `<principal-id>` a parancsban a szolgáltatásnév `az webapp identity assign` azonosítójával
    - `<registry-name>` a tároló-beállításjegyzék nevével
    - `<subscription-id>` a parancsból lekért `az account show` előfizetés-azonosítóval

További információ ezekről az engedélyekről: [Mi az az Azure szerepköralapú hozzáférés-vezérlés és](../role-based-access-control/overview.md) 

## <a name="deploy-the-image-and-test-the-app"></a>A rendszerkép üzembe helyezése és az alkalmazás tesztelése

Ezeket a lépéseket akkor használhatja, ha a rendszerkép le van küldve a tároló-beállításjegyzékbe, és App Service rendszerkép teljes mértékben ki van építve.

1. Az [`az webapp config container set`](/cli/azure/webapp/config/container#az_webapp_config_container_set) paranccsal adhatja meg a tároló-beállításjegyzéket és a webalkalmazáshoz üzembe helyező rendszerképet:

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group AppSvc-DockerTutorial-rg --docker-custom-image-name <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest --docker-registry-server-url https://<registry-name>.azurecr.io
    ```
    
    Cserélje `<app_name>` le a helyére a webalkalmazás nevét, a helyére pedig két helyen a regisztrációs adatbázis `<registry-name>` nevét. 

    - Ha a beállításjegyzéket nem Docker Hub (ahogy a példában is látható), a formátumát a beállításjegyzék teljes tartománynevének megfelelően `--docker-registry-server-url` `https://` kell formázni.
    - A következő üzenet jelenik meg: "Nem adott meg hitelesítő adatokat a Azure Container Registry. Keresni próbál..." A azt jelzi, hogy az Azure az alkalmazás felügyelt identitását használja a tároló-beállításjegyzékben való hitelesítéshez, és nem kér felhasználónevet és jelszót.
    - Ha a "AttributeError: "NoneType" objektum nem rendelkezik "reserved" attribútummal, ellenőrizze, hogy `<app-name>` helyes-e.

    > [!TIP]
    > A webalkalmazás tárolóbeállítását bármikor lekérheti a `az webapp config container show --name <app-name> --resource-group AppSvc-DockerTutorial-rg` paranccsal. A rendszerkép a tulajdonságban van `DOCKER_CUSTOM_IMAGE_NAME` megadva. Ha a webalkalmazást az Azure DevOps vagy Azure Resource Manager használatával telepíti, a rendszerkép egy nevű tulajdonságban is `LinuxFxVersion` megjelenhet. Mindkét tulajdonság ugyanazt a célt szolgálja. Ha mindkettő jelen van a webalkalmazás konfigurációjában, a `LinuxFxVersion` elsőbbséget élvez.

1. A parancs befejeződik, a webalkalmazásnak futnia kell a tárolóban `az webapp config container set` a App Service.

    Az alkalmazás teszteléséhez nyissa meg a webhelyet, és cserélje le a et `http://<app-name>.azurewebsites.net` `<app-name>` a webalkalmazás nevére. Az első hozzáféréskor az alkalmazásnak némi időre van szükség, mert App Service a teljes rendszerképet le kell vennie a regisztrációs adatbázisból. Ha a böngésző időkorrekta, egyszerűen frissítse az oldalt. A kezdeti rendszerkép lekért futtatása után a további tesztek sokkal gyorsabban fognak futni.

    ![A webalkalmazás sikeres tesztelése az Azure-ban](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="modify-the-app-code-and-redeploy"></a>Az alkalmazás kódának módosítása és ismételt üzembe állás

Ebben a szakaszban módosítja a webalkalmazás kódját, újraépíti a tárolót, majd lekultálja a tárolót a regisztrációs adatbázisba. App Service le a frissített rendszerképet a beállításjegyzékből a futó webalkalmazás frissítéséhez.

1. A helyi *docker-django-webapp-linux* mappában nyissa meg az *app/templates/app/index.htmfájlt.*

1. Módosítsa az első HTML-elemet úgy, hogy az megfeleljen az alábbi kódnak.

    ```html
    <nav class="navbar navbar-inverse navbar-fixed-top">
      <div class="container">
        <div class="navbar-header">
          <a class="navbar-brand" href="#">Azure App Service - Updated Here!</a>
        </div>
      </div>
    </nav>
    ```
    
1. Mentse a módosításokat.

1. Váltsa át a *docker-django-webapp-linux* mappát, és építse újra a rendszerképet:

    ```bash
    docker build --tag appsvc-tutorial-custom-image .
    ```

1. Frissítse a rendszerkép címkében a verziószámot az 1.0.1-es verzióra:

    ```bash
    docker tag appsvc-tutorial-custom-image <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

    Cserélje le a `<registry-name>` elemet a tárolójegyzék nevére.

1. A rendszerkép leküldése a beállításjegyzékbe:

    ```bash
    docker push <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

1. Indítsa újra a webalkalmazást:

    ```azurecli-interactive
    az webapp restart --name <app_name> --resource-group AppSvc-DockerTutorial-rg
    ```

    A `<app_name>` rész helyére a webalkalmazás nevét írja be. Újraindításkor a App Service le a frissített rendszerképet a tároló-beállításjegyzékből.

1. Ellenőrizze, hogy a frissítés telepítve lett-e. Ezt a következő oldal egyikében ellenőrizheti: `http://<app-name>.azurewebsites.net` .

## <a name="access-diagnostic-logs"></a>Diagnosztikai naplók elérése

1. A tárolónaplózás bekapcsolása:

    ```azurecli-interactive
    az webapp log config --name <app-name> --resource-group AppSvc-DockerTutorial-rg --docker-container-logging filesystem
    ```
    
1. Engedélyezze a naplóstreamet:

    ```azurecli-interactive
    az webapp log tail --name <app-name> --resource-group AppSvc-DockerTutorial-rg
    ```
    
    Ha nem jelennek meg azonnal a konzolnaplófájlok, ellenőrizze ismét 30 másodperc múlva.

    A naplófájlokat a böngészőből is megtekintheti a következő címen: `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

1. A naplóstreamelést bármikor leállíthatja a **Ctrl** C + **billentyűkombinációval.**

## <a name="connect-to-the-container-using-ssh"></a>Csatlakozás a tárolóhoz SSH használatával

Az SSH lehetővé teszi a tároló és az ügyfél közötti biztonságos kommunikációt. A tárolóhoz való SSH-kapcsolat engedélyezéséhez konfigurálni kell hozzá az egyéni rendszerképet. Ha a tároló már fut, megnyithat egy SSH-kapcsolatot.

### <a name="configure-the-container-for-ssh"></a>A tároló konfigurálása SSH-hez

Az oktatóanyagban használt mintaalkalmazás már rendelkezik a szükséges konfigurációval a *Dockerfile-ban,* amely telepíti az SSH-kiszolgálót, és beállítja a bejelentkezési hitelesítő adatokat. Ez a szakasz csak tájékoztató. A tárolóhoz való csatlakozáshoz ugorjon a következő szakaszra

```Dockerfile
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
  && apt-get install -y --no-install-recommends openssh-server \
  && echo "$SSH_PASSWD" | chpasswd 
```

> [!NOTE]
> Ez a konfiguráció nem engedélyezi a tárolóhoz való külső kapcsolatokat. Az SSH csak a Kudu/SCM webhelyen keresztül érhető el. A Kudu/SCM webhely hitelesítése az Ön Azure-fiókjával történik.

A *Dockerfile* a sshd_config *az* */etc/ssh/* mappába másolja, és elérhetővé teszi a tároló 2222-es portját:

```Dockerfile
COPY sshd_config /etc/ssh/

# ...

EXPOSE 8000 2222
```

A 2222-es port egy belső port, amelyet csak a privát virtuális hálózat hídhálózatán belüli tárolók érhetők el. 

Végül a bejegyzés parancsfájlja (a *init.sh* elindítja az SSH-kiszolgálót.

```bash
#!/bin/bash
service ssh start
```

### <a name="open-ssh-connection-to-container"></a>A tároló SSH-kapcsolatának megnyitása

1. Nyissa meg `https://<app-name>.scm.azurewebsites.net/webssh/host` a webhelyet, és jelentkezzen be Azure-fiókjával. A `<app-name>` rész helyére a webalkalmazás nevét írja be.

1. Miután bejelentkezett, a rendszer átirányítja a webalkalmazás információs oldalára. Az oldal tetején válassza az **SSH** lehetőséget a rendszerhéj megnyitásához és a parancsok használathoz.

    Például megvizsgálhatja a benne futó folyamatokat az `top` paranccsal.
    
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az ebben a cikkben létrehozott erőforrások folyamatos költségekkel is járhatnak. Az erőforrások törléséhez csak az azokat tartalmazó erőforráscsoportot kell törölnie:

```azurecli
az group delete --name AppSvc-DockerTutorial-rg
```

::: zone-end

## <a name="next-steps"></a>Következő lépések

Az alábbiak elvégzését ismerte meg:

> [!div class="checklist"]
> * Egyéni rendszerkép üzembe helyezése privát tároló-beállításjegyzékben
> * Üzembe helyezés és az egyéni rendszerkép a App Service
::: zone pivot="container-linux"
> * A rendszerkép frissítése és ismételt üzembeása
::: zone-end
> * Diagnosztikai naplók elérése
::: zone pivot="container-linux"
> * Csatlakozás a tárolóhoz SSH használatával
::: zone-end

A következő oktatóanyagból megtudhatja, hogyan lehet leképezni egy egyéni DNS-nevet az alkalmazáshoz.

> [!div class="nextstepaction"]
> [Oktatóanyag: Egyéni DNS-név leképezése az alkalmazásra](app-service-web-tutorial-custom-domain.md)

Vagy tekintse meg az egyéb erőforrásokat:

> [!div class="nextstepaction"]
> [Egyéni tároló konfigurálása](configure-custom-container.md)

::: zone pivot="container-linux"
> [!div class="nextstepaction"]
> [Oktatóanyag: Többtárolós WordPress-alkalmazás](tutorial-multi-container-app.md)
::: zone-end

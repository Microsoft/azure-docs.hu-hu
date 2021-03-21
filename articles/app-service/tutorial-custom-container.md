---
title: 'Oktatóanyag: egyéni rendszerkép létrehozása és futtatása Azure App Service'
description: Lépésenkénti útmutató egyéni Linux-vagy Windows-lemezképek létrehozásához, a lemezkép Azure Container Registry való elküldéséhez, majd a lemezkép központi telepítéséhez Azure App Service. Megtudhatja, hogyan telepítheti át az egyéni szoftverek telepítését App Service egy egyéni tárolóban.
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: msangapu
keywords: Azure app Service, webalkalmazás, Linux, Windows, Docker, tároló
ms.custom: devx-track-csharp, mvc, seodec18, devx-track-python, devx-track-azurecli
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 5d3a714230f0279bd68b39cd02624866b9b3bacf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102180513"
---
# <a name="migrate-custom-software-to-azure-app-service-using-a-custom-container"></a>Egyéni szoftver migrálása Azure App Service egyéni tároló használatával

::: zone pivot="container-windows"  

Az [Azure App Service](overview.md) előre meghatározott, IIS-en futó alkalmazáscsoportokat biztosít Windows rendszeren, például az ASP.NET-et vagy a Node.js-t. Az előre konfigurált Windows-környezet letiltja az operációs rendszeren például a rendszergazdai hozzáférést, a szoftvertelepítést vagy a globális szerelvény-gyorsítótár módosítását (lásd [az operációs rendszer Azure App Service-funkcionalitásával foglalkozó részt](operating-system-functionality.md)). Egy egyéni Windows-tároló az App Service-ben való használata lehetővé teszi az alkalmazáshoz szükséges operációs rendszer-módosítások végrehajtását, így egyszerűen áttelepítheti az egyéni operációsrendszer- és szoftverkonfigurációkat igénylő helyszíni alkalmazásokat. Ez az oktatóanyag bemutatja egy Windows betűkészlet-könyvtárba telepített egyéni betűkészletet használó ASP.NET-alkalmazás az App Service-be való áttelepítésének menetét. Egy Visual Studióból származó egyénileg konfigurált Windows-rendszerképet kell üzembe helyeznie az [Azure Container Registryben](../container-registry/index.yml), majd futtatnia az App Service-ben.

![Megjeleníti a Windows-tárolóban futó webalkalmazást.](media/tutorial-custom-container/app-running.png)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

- <a href="https://hub.docker.com/" target="_blank">Regisztráció Docker Hub-fiókra</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">Windows rendszerhez készült Docker telepítése</a>.
- <a href="/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">A Docker átváltása Windows-tárolók futtatására</a>.
- <a href="https://www.visualstudio.com/downloads/" target="_blank">Telepítse a Visual Studio 2019</a> -et a **ASP.net, valamint a webes fejlesztési** és az **Azure-fejlesztési** számítási feladatokkal. Ha már telepítette a Visual Studio 2019-et:
    - A   >  **frissítések keresése** lehetőségre kattintva telepítse a legújabb frissítéseket a Visual Studióban.
    - Adja hozzá a számítási feladatokat a Visual Studióban **az eszközök**  >  **beolvasása eszközök és szolgáltatások lekérése** elemre kattintva.

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

:::image type="content" source="media/tutorial-custom-container/local-app-in-browser.png" alt-text="Az alapértelmezett böngészőben megjelenített alkalmazást ábrázoló képernyőkép.":::

Az alkalmazás nem futtatható az App Service tesztkörnyezetben, mert egy telepített betűkészletet használ. Egy Windows-tároló használatával viszont üzembe helyezheti, mert a betűkészlet a Windows-tárolóban telepíthető.

### <a name="configure-windows-container"></a>Windows-tároló konfigurálása

A Megoldáskezelőben kattintson jobb gombbal a **CustomFontSample** projektre, és válassza az **Add** > **Container Orchestration Support** (Hozzáadás > Container Orchestration Support) lehetőséget.

:::image type="content" source="media/tutorial-custom-container/enable-container-orchestration.png" alt-text="Képernyőkép a Megoldáskezelő ablakról, amely a CustomFontSample projektet, a hozzáadást és a tároló Orchestrator támogató menüelemeket mutatja.":::

Válassza a **Docker-összeállítás**  >  **OK** elemet.

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

:::image type="content" source="media/tutorial-custom-container/open-publish-wizard.png" alt-text="Képernyőfelvétel a CustomFontSample-projektről és a közzétenni kívánt Megoldáskezelőról.":::

### <a name="create-registry-and-publish"></a>Beállításjegyzék létrehozása és közzététele

A Közzétételi varázslóban válassza a **Container Registry**  >  **új Azure Container Registry**  >  **Közzététel** lehetőséget.

:::image type="content" source="media/tutorial-custom-container/create-registry.png" alt-text="A közzétételi varázsló képernyőképe, amelyen Container Registry, új Azure Container Registry létrehozása és a közzététel gomb van kiválasztva.":::

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

A bal oldali menüben válassza az **erőforrás létrehozása**  >  **webes**  >  **Web App for containers** elemet.

### <a name="configure-app-basics"></a>Alkalmazás alapalapjainak konfigurálása

Az **alapvető** beállítások lapon konfigurálja a beállításokat az alábbi táblázat szerint, majd kattintson a Tovább gombra **: Docker**.

| Beállítás  | Ajánlott érték | További tudnivalók |
| ----------------- | ------------ | ----|
|**Előfizetés**| Győződjön meg arról, hogy a megfelelő előfizetés van kiválasztva. |  |
|**Erőforráscsoport**| Válassza az **új létrehozása** elemet, írja be a **myResourceGroup**, majd kattintson **az OK** gombra. |  |
|**Név**| Írjon be egy egyedi nevet. | A webalkalmazás URL-címe `http://<app-name>.azurewebsites.net`, amelyben az `<app-name>` az alkalmazás neve. |
|**Közzététel**| Docker-tároló | |
|**Operációs rendszer**| Windows | |
|**Régió**| Nyugat-Európa | |
|**Windows-csomag**| Válassza az **új létrehozása** elemet, írja be a **myAppServicePlan**, majd kattintson **az OK** gombra. | |

Az **alapvető beállítások** lap így néz ki:

![A webalkalmazás konfigurálásához használt alapjai lapot jeleníti meg.](media/tutorial-custom-container/configure-app-basics.png)

### <a name="configure-windows-container"></a>Windows-tároló konfigurálása

A **Docker** lapon konfigurálja az egyéni Windows-tárolót az alábbi táblázatban látható módon, majd válassza a **felülvizsgálat + létrehozás** elemet.

| Beállítás  | Ajánlott érték |
| ----------------- | ------------ |
|**Kép forrása**| Azure Container-regisztráció |
|**Registry**| Válassza ki [a korábban létrehozott beállításjegyzéket](#publish-to-azure-container-registry). |
|**Kép**| customfontsample |
|**Tag**| legújabb |

### <a name="complete-app-creation"></a>Alkalmazás létrehozásának befejezése

Kattintson a **Létrehozás** parancsra, majd várjon, amíg az Azure létrehozza a szükséges erőforrásokat.

## <a name="browse-to-the-web-app"></a>A webalkalmazás kikeresése

Értesítés jelenik meg, ha az Azure befejezte a művelet végrehajtását.

![Azt mutatja, hogy az Azure-művelet befejeződött.](media/tutorial-custom-container/portal-create-finished.png)

1. Kattintson az **Erőforrás megnyitása** lehetőségre.

2. Az alkalmazás oldalán kattintson az **URL-cím** mező alatti hivatkozásra.

Egy új böngészőlapon a következő oldal jelenik meg:

![Megjeleníti a webalkalmazás új böngésző oldalát.](media/tutorial-custom-container/app-starting.png)

Várjon néhány percet, és próbálkozzon újra, amíg meg nem jelenik a kezdőlap a várt, gyönyörű betűtípussal:

![Megjeleníti a megadott betűkészlettel rendelkező kezdőlapot.](media/tutorial-custom-container/app-running.png)

**Gratulálunk!** Áttelepített egy ASP.NET-alkalmazást az Azure App Service szolgáltatásba egy Windows-tárolóban.

## <a name="see-container-start-up-logs"></a>A tároló rendszerindítási naplóinak megtekintése

A Windows-tároló betöltése hosszabb időbe telhet. Ha szeretné megtekinteni a folyamatot, keresse meg a következő URL-címet, *\<app-name>* és cserélje le az alkalmazás nevét.
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

A Azure App Service a Docker-tároló technológiáját használja a beépített rendszerképek és az Egyéni rendszerképek üzemeltetéséhez. A beépített rendszerképek listájának megtekintéséhez futtassa az ["az WebApp List-Runtimes--Linux"](/cli/azure/webapp#az-webapp-list-runtimes)nevű Azure CLI-parancsot. Ha ezek a képek nem felelnek meg az igényeinek, létrehozhat és üzembe helyezhet egyéni lemezképeket.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy egyéni rendszerképet, ha a beépített rendszerkép nem elégíti ki az igényeinek megfelelőt
> * Az egyéni rendszerkép leküldése az Azure-beli privát tároló-beállításjegyzékbe
> * Az egyéni rendszerkép futtatása App Service
> * Környezeti változók konfigurálása
> * A rendszerkép frissítése és újbóli üzembe helyezése
> * Diagnosztikai naplók elérése
> * Csatlakozás a tárolóhoz SSH használatával

Az oktatóanyag elvégzésével kis díjat számítunk fel az Azure-fiókjában a Container Registry számára, és további költségekkel jár, ha a tárolót több mint egy hónap alatt üzemelteti.

## <a name="set-up-your-initial-environment"></a>A kezdeti környezet beállítása

- Rendelkeznie kell aktív előfizetéssel rendelkező Azure-fiókkal. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Telepítse a [Docker](https://docs.docker.com/get-started/#setup)-t, amelyet Docker-rendszerképek létrehozásához használ. A Docker telepítéséhez szükség lehet a számítógép újraindítására.
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
- Az oktatóanyaghoz az Azure CLI 2.0.80 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

A Docker telepítése vagy a Azure Cloud Shell futtatása után nyisson meg egy terminál ablakot, és ellenőrizze, hogy telepítve van-e a Docker:

```bash
docker --version
```

## <a name="clone-or-download-the-sample-app"></a>A minta alkalmazás klónozása vagy letöltése

Ehhez az oktatóanyaghoz a git-klón vagy a letöltés használatával szerezheti be a mintát.

### <a name="clone-with-git"></a>Klónozás a git-vel

A minta tárház klónozása:

```terminal
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
```

Ügyeljen arra, hogy tartalmazza az `--config core.autocrlf=input` argumentumot, amely garantálja a megfelelő sorok befejezését a Linux-tárolón belül használt fájlokban:

Ezután nyissa meg a mappát:

```terminal
cd docker-django-webapp-linux
```

### <a name="download-from-github"></a>Letöltés a GitHubról

A git-klón használata helyett látogasson el a klónozás lehetőségre, [https://github.com/Azure-Samples/docker-django-webapp-linux](https://github.com/Azure-Samples/docker-django-webapp-linux) majd válassza a **zip letöltése** lehetőséget.  

Csomagolja ki a ZIP-fájlt egy *Docker-Django-WebApp-Linux* nevű mappába. 

Ezután nyisson meg egy terminál-ablakot a *Docker-Django-WebApp-Linux* mappában.

## <a name="optional-examine-the-docker-file"></a>Választható A Docker-fájl vizsgálata

A _Docker_ nevű mintában szereplő fájl, amely leírja a Docker-rendszerképet, és konfigurációs utasításokat tartalmaz:

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

* A parancsok első csoportja telepíti az alkalmazás követelményeit a környezetben.
* A második parancs egy [SSH](https://www.ssh.com/ssh/protocol/) -kiszolgálót hoz létre a tároló és a gazdagép közötti biztonságos kommunikációhoz.
* Az utolsó sor, `ENTRYPOINT ["init.sh"]` amely elindítja `init.sh` az SSH-szolgáltatást és a Python-kiszolgálót.

## <a name="build-and-test-the-image-locally"></a>A rendszerkép helyi létrehozása és tesztelése

> [!NOTE]
> A Docker hub [kvótákat használ az IP-címekhez tartozó névtelen lekérések számával és az ingyenes felhasználók által hitelesített lekérések számával (lásd az **adatátvitelt**)](https://www.docker.com/pricing). Ha azt tapasztalja, hogy a Docker hub-ból való lekérések korlátozottak, próbálja meg, `docker login` Ha még nincs bejelentkezve.
> 

1. A rendszerkép létrehozásához futtassa a következő parancsot:

    ```bash
    docker build --tag appsvc-tutorial-custom-image .
    ```
    
1. Ellenőrizze, hogy a Build működik-e a Docker-tároló helyi futtatásával:

    ```bash
    docker run -p 8000:8000 appsvc-tutorial-custom-image
    ```
    
    Ez [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) a parancs az argumentumot tartalmazó portot adja meg, `-p` amelyet a rendszerkép neve követ. 
    
    > [!TIP]
    > Ha Windows rendszeren fut, és megtekinti a hibát, *standard_init_linux. go: 211: az exec felhasználói folyamata "nincs ilyen fájl vagy könyvtár"*, a *init.sh* -fájl a várt LF végződés helyett a CR-LF sorok végét tartalmazza. Ez a hiba akkor fordul elő, ha a git használatával klónozott a minta tárházat, de kihagyta a `--config core.autocrlf=input` paramétert. Ebben az esetben a tárházat a "--config" argumentummal újra klónozással. A hiba akkor is megjelenhet, ha szerkesztette a *init.sh* , és CRLF-végződésekkel mentette. Ebben az esetben mentse újra a fájlt csak LF végződéssel.

1. `http://localhost:8000`A webalkalmazás és a tároló megfelelő működésének ellenőrzéséhez keresse fel a következőt:.

    ![Webalkalmazás helyi tesztelése](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Ebben a szakaszban és a következő lépésekben olyan erőforrásokat kell kiépíteni az Azure-ban, amelyeken leküldi a lemezképet, majd üzembe helyezi a tárolót Azure App Service. Először hozzon létre egy erőforráscsoportot, amelyben az összes erőforrást össze szeretné gyűjteni.

Futtassa az az [Group Create](/cli/azure/group#az-group-create) parancsot egy erőforráscsoport létrehozásához:

```azurecli-interactive
az group create --name AppSvc-DockerTutorial-rg --location westus2
```

Az érték módosításával `--location` megadhatja az Ön közelében lévő régiót.

## <a name="push-the-image-to-azure-container-registry"></a>A rendszerkép leküldése Azure Container Registry

Ebben a szakaszban leküldi a rendszerképet arra a Azure Container Registry, amelyről a App Service telepítheti.

1. [`az acr create`](/cli/azure/acr#az-acr-create)Azure Container Registry létrehozásához futtassa a parancsot:

    ```azurecli-interactive
    az acr create --name <registry-name> --resource-group AppSvc-DockerTutorial-rg --sku Basic --admin-enabled true
    ```
    
    Cserélje le a helyére a `<registry-name>` beállításjegyzék megfelelő nevét. A név csak betűket és számokat tartalmazhat, és egyedinek kell lennie az összes Azure-ban.

1. Futtassa a [`az acr show`](/cli/azure/acr#az-acr-show) parancsot a beállításjegyzék hitelesítő adatainak lekéréséhez:

    ```azurecli-interactive
    az acr credential show --resource-group AppSvc-DockerTutorial-rg --name <registry-name>
    ```
    
    A parancs JSON-kimenete két jelszót biztosít a beállításjegyzék felhasználónevével együtt.
    
1. A `docker login` parancs használatával jelentkezzen be a tároló-beállításjegyzékbe:

    ```bash
    docker login <registry-name>.azurecr.io --username <registry-username>
    ```
    
    Cserélje `<registry-name>` le `<registry-username>` az és az értékeket az előző lépésekből származó értékekre. Ha a rendszer kéri, írja be az előző lépésben szereplő jelszavak egyikét.

    Ugyanazt a beállításjegyzék-nevet használja a szakasz összes hátralévő lépésében.

1. Miután a bejelentkezés sikeres volt, címkézze meg a beállításjegyzék helyi Docker-rendszerképét:

    ```bash
   docker tag appsvc-tutorial-custom-image <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```    

1. A következő `docker push` paranccsal küldje le a rendszerképet a beállításjegyzékbe:

    ```bash
    docker push <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

    A rendszerkép feltöltése az első alkalommal eltarthat néhány percig, mert az kiinduló képet tartalmazza. A későbbi feltöltések általában gyorsabbak.

    Várakozás közben a következő szakaszban ismertetett lépések végrehajtásával konfigurálhatja a App Servicet a beállításjegyzékből való telepítéshez.

1. A `az acr repository list` parancs használatával ellenőrizze, hogy a leküldése sikeres volt-e:

    ```azurecli-interactive
    az acr repository list -n <registry-name>
    ```
    
    A kimenetnek meg kell jelenítenie a rendszerkép nevét.


## <a name="configure-app-service-to-deploy-the-image-from-the-registry"></a>App Service konfigurálása a rendszerkép beállításjegyzékből való üzembe helyezéséhez

Azure App Service tároló üzembe helyezéséhez először létre kell hoznia egy webalkalmazást a App Service, majd a webalkalmazást a tároló-beállításjegyzékhez kell kötni. A webalkalmazás indításakor App Service automatikusan lekéri a rendszerképet a beállításjegyzékből.

1. Hozzon létre egy App Service tervet a [`az appservice plan create`](/cli/azure/appservice/plan#az-appservice-plan-create) parancs használatával:

    ```azurecli-interactive
    az appservice plan create --name AppSvc-DockerTutorial-plan --resource-group AppSvc-DockerTutorial-rg --is-linux
    ```

    A App Service-csomag megfelel a webalkalmazást futtató virtuális gépnek. Alapértelmezés szerint az előző parancs egy olcsó B1-es [díjszabási szintet](https://azure.microsoft.com/pricing/details/app-service/linux/) használ, amely az első hónapban ingyenes. A szintet a paraméterrel szabályozhatja `--sku` .

1. Hozza létre a webalkalmazást a következő [`az webpp create`](/cli/azure/webapp#az-webapp-create) paranccsal:

    ```azurecli-interactive
    az webapp create --resource-group AppSvc-DockerTutorial-rg --plan AppSvc-DockerTutorial-plan --name <app-name> --deployment-container-image-name <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```
    
    Cserélje le a `<app-name>` nevet a webalkalmazás nevére, amelynek egyedinek kell lennie az összes Azure-ban. Cserélje le a `<registry-name>` elemet az előző szakaszban szereplő beállításjegyzék nevére is.

1. A [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) `WEBSITES_PORT` környezeti változó az alkalmazás kódjának megfelelően történő beállításához használja a következőt: 

    ```azurecli-interactive
    az webapp config appsettings set --resource-group AppSvc-DockerTutorial-rg --name <app-name> --settings WEBSITES_PORT=8000
    ```

    Cserélje le az `<app-name>` nevet az előző lépésben használt névre.
    
    A környezeti változóval kapcsolatos további információkért tekintse [meg a minta GitHub-tárházában található Readme](https://github.com/Azure-Samples/docker-django-webapp-linux)témakört.

1. [Felügyelt identitás](./overview-managed-identity.md) engedélyezése a webalkalmazás számára a következő [`az webapp identity assign`](/cli/azure/webapp/identity#az-webapp-identity-assign) paranccsal:

    ```azurecli-interactive
    az webapp identity assign --resource-group AppSvc-DockerTutorial-rg --name <app-name> --query principalId --output tsv
    ```

    Cserélje le az `<app-name>` nevet az előző lépésben használt névre. A parancs kimenete (a és az argumentumok alapján szűrve `--query` `--output` ) a hozzárendelt identitás egyszerű szolgáltatása, amelyet hamarosan használ.

    A felügyelt identitás lehetővé teszi, hogy engedélyeket adjon a webalkalmazásnak más Azure-erőforrások eléréséhez, anélkül, hogy konkrét hitelesítő adatokat kellene megadnia.

1. Kérje le az előfizetés-azonosítót a [`az account show`](/cli/azure/account#az-account-show) paranccsal, amelyet a következő lépésben kell megadnia:

    ```azurecli-interactive
    az account show --query id --output tsv
    ``` 

1. Adja meg a webalkalmazás engedélyt a tároló beállításjegyzékének eléréséhez:

    ```azurecli-interactive
    az role assignment create --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourceGroups/AppSvc-DockerTutorial-rg/providers/Microsoft.ContainerRegistry/registries/<registry-name> --role "AcrPull"
    ```

    Cserélje le a következő értékeket:
    - `<principal-id>` a szolgáltatás egyszerű azonosítója a `az webapp identity assign` parancsból
    - `<registry-name>` a tároló-beállításjegyzék nevével
    - `<subscription-id>`a parancsból beolvasott előfizetés-AZONOSÍTÓval `az account show`

További információ ezekről az engedélyekről: [Mi az Azure szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md) és 

## <a name="deploy-the-image-and-test-the-app"></a>A rendszerkép üzembe helyezése és az alkalmazás tesztelése

Ezeket a lépéseket akkor hajthatja végre, ha a rendszerkép leküldése a tároló-beállításjegyzékbe történik, és a App Service teljesen kiépítve.

1. A [`az webapp config container set`](/cli/azure/webapp/config/container#az-webapp-config-container-set) paranccsal adhatja meg a tároló-beállításjegyzéket és a webalkalmazáshoz telepítendő lemezképet:

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group AppSvc-DockerTutorial-rg --docker-custom-image-name <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest --docker-registry-server-url https://<registry-name>.azurecr.io
    ```
    
    Cserélje le a `<app_name>` nevet a webalkalmazás nevére, és cserélje le `<registry-name>` két helyen a beállításjegyzék nevével. 

    - Ha a Docker hub-tól eltérő beállításjegyzéket használ (ebben a példában látható), akkor `--docker-registry-server-url` `https://` a beállításjegyzék teljes tartományneve után kell formázni.
    - A következő üzenet jelenik meg: "nincs hitelesítő adat a Azure Container Registry eléréséhez. Kísérlet a keresésre... " azt jelzi, hogy az Azure az alkalmazás felügyelt identitását használja a tároló beállításjegyzékének hitelesítéséhez, és nem kér felhasználónevet és jelszót.
    - Ha hibát tapasztal, a "AttributeError:" NoneType "objektumnak nincs" foglalt "attribútuma, ellenőrizze, hogy `<app-name>` helyes-e.

    > [!TIP]
    > A webalkalmazás tárolójának beállításait bármikor lekérheti a paranccsal `az webapp config container show --name <app-name> --resource-group AppSvc-DockerTutorial-rg` . A rendszerkép a tulajdonságban van megadva `DOCKER_CUSTOM_IMAGE_NAME` . Ha a webalkalmazás üzembe helyezése az Azure DevOps vagy a Azure Resource Manager-sablonokon keresztül történik, akkor a lemezkép a nevű tulajdonságban is megjelenhet `LinuxFxVersion` . Mindkét tulajdonság ugyanazt a célt szolgálja. Ha mindkettő megtalálható a webalkalmazás konfigurációjában, `LinuxFxVersion` elsőbbséget élvez.

1. A `az webapp config container set` parancs végrehajtása után a webalkalmazásnak a app Service tárolójában kell futnia.

    Az alkalmazás teszteléséhez keresse meg a `http://<app-name>.azurewebsites.net` következőt:, és cserélje le a kifejezést `<app-name>` a webalkalmazás nevére. Először is eltarthat egy ideig, amíg az alkalmazás válaszolni tud, mert App Service a teljes rendszerképet a beállításjegyzékből kell lekérnie. Ha a böngésző túllépi az időkorlátot, csak frissítse az oldalt. A kezdeti rendszerkép kihúzása után a további tesztek sokkal gyorsabban futnak.

    ![Az Azure-beli webalkalmazás sikeres tesztelése](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="modify-the-app-code-and-redeploy"></a>Az alkalmazás kódjának módosítása és újbóli üzembe helyezése

Ebben a szakaszban módosítja a webalkalmazás kódját, újraépíti a tárolót, majd leküldi a tárolót a beállításjegyzékbe. App Service ezután automatikusan lekéri a frissített rendszerképet a beállításjegyzékből a futó webalkalmazás frissítéséhez.

1. A helyi *Docker-Django-WebApp-Linux* mappában Nyissa meg a file *app/templates/app/index.html* fájlt.

1. Módosítsa az első HTML-elemet úgy, hogy az megfeleljen a következő kódnak.

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

1. Váltson a *Docker-Django-WebApp-Linux* mappára, és hozza létre újra a rendszerképet:

    ```bash
    docker build --tag appsvc-tutorial-custom-image .
    ```

1. Frissítse a rendszerkép címkéjén található verziószámot v 1.0.1-re:

    ```bash
    docker tag appsvc-tutorial-custom-image <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

    Cserélje le a `<registry-name>` elemet a tárolójegyzék nevére.

1. Küldje le a rendszerképet a beállításjegyzékbe:

    ```bash
    docker push <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

1. Indítsa újra a webalkalmazást:

    ```azurecli-interactive
    az webapp restart --name <app_name> --resource-group AppSvc-DockerTutorial-rg
    ```

    A `<app_name>` rész helyére a webalkalmazás nevét írja be. Újraindítás után App Service lekéri a frissített rendszerképet a tároló-beállításjegyzékből.

1. A tallózással ellenőrizze, hogy a frissítés telepítve van-e `http://<app-name>.azurewebsites.net` .

## <a name="access-diagnostic-logs"></a>Diagnosztikai naplók elérése

1. A tároló naplózásának bekapcsolása:

    ```azurecli-interactive
    az webapp log config --name <app-name> --resource-group AppSvc-DockerTutorial-rg --docker-container-logging filesystem
    ```
    
1. A log stream engedélyezése:

    ```azurecli-interactive
    az webapp log tail --name <app-name> --resource-group AppSvc-DockerTutorial-rg
    ```
    
    Ha nem jelennek meg azonnal a konzolnaplófájlok, ellenőrizze ismét 30 másodperc múlva.

    A naplófájlokat a böngészőből is megtekintheti a következő címen: `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

1. Ha bármikor le szeretné állítani a naplózási adatfolyamot, írja be a **CTRL C billentyűt** + .

## <a name="connect-to-the-container-using-ssh"></a>Csatlakozás a tárolóhoz SSH használatával

Az SSH lehetővé teszi a tároló és az ügyfél közötti biztonságos kommunikációt. A tárolóhoz való SSH-kapcsolatok engedélyezéséhez konfigurálnia kell az egyéni rendszerképet. Miután a tároló fut, megnyithat egy SSH-kapcsolatokat.

### <a name="configure-the-container-for-ssh"></a>Az SSH-tároló konfigurálása

Az oktatóanyagban használt minta alkalmazás már rendelkezik a szükséges konfigurációval a *Docker*, amely telepíti az SSH-kiszolgálót, és beállítja a bejelentkezési hitelesítő adatokat is. Ez a szakasz csak tájékoztató információkat tartalmaz. A tárolóhoz való kapcsolódáshoz ugorjon a következő szakaszra

```Dockerfile
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
  && apt-get install -y --no-install-recommends openssh-server \
  && echo "$SSH_PASSWD" | chpasswd 
```

> [!NOTE]
> Ez a konfiguráció nem engedélyezi a külső kapcsolatokat a tárolóval. Az SSH csak a Kudu/SCM webhelyen keresztül érhető el. Az kudu/SCM-hely hitelesítése az Azure-fiókkal történik.

A *Docker* az *sshd_config* fájlt is átmásolja a */etc/ssh/* mappába, és az 2222-es portot a tárolón teszi elérhetővé:

```Dockerfile
COPY sshd_config /etc/ssh/

# ...

EXPOSE 8000 2222
```

Az 2222-es port egy belső port, amely csak a privát virtuális hálózathoz tartozó Bridge hálózaton lévő tárolók számára érhető el. 

Végül, a *init.sh*, elindítja az SSH-kiszolgálót.

```bash
#!/bin/bash
service ssh start
```

### <a name="open-ssh-connection-to-container"></a>A tároló SSH-kapcsolatának megnyitása

1. Keresse meg `https://<app-name>.scm.azurewebsites.net/webssh/host` és jelentkezzen be az Azure-fiókjával. A `<app-name>` rész helyére a webalkalmazás nevét írja be.

1. Miután bejelentkezett, a rendszer átirányítja a webalkalmazáshoz tartozó információs oldalra. Válassza az **SSH** lehetőséget az oldal tetején a rendszerhéj megnyitásához és a parancsok használatához.

    Megvizsgálhatja például a-ben futó folyamatokat a parancs használatával `top` .
    
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az ebben a cikkben létrehozott erőforrások folyamatos költségeket okozhatnak. az erőforrások törléséhez csak az azokat tartalmazó erőforráscsoportot kell törölnie:

```azurecli
az group delete --name AppSvc-DockerTutorial-rg
```

::: zone-end

## <a name="next-steps"></a>Következő lépések

Az alábbiak elvégzését ismerte meg:

> [!div class="checklist"]
> * Egyéni rendszerkép üzembe helyezése egy privát tároló beállításjegyzékében
> * Üzembe helyezés és az egyéni lemezkép App Service
::: zone pivot="container-linux"
> * A rendszerkép frissítése és újbóli üzembe helyezése
::: zone-end
> * Diagnosztikai naplók elérése
::: zone pivot="container-linux"
> * Csatlakozás a tárolóhoz SSH használatával
::: zone-end

A következő oktatóanyagban megismerheti, hogyan képezhető le egyéni DNS-név az alkalmazáshoz.

> [!div class="nextstepaction"]
> [Oktatóanyag: egyéni DNS-név leképezése az alkalmazáshoz](app-service-web-tutorial-custom-domain.md)

Vagy tekintse meg a többi erőforrást:

> [!div class="nextstepaction"]
> [Egyéni tároló konfigurálása](configure-custom-container.md)

::: zone pivot="container-linux"
> [!div class="nextstepaction"]
> [Oktatóanyag: Multi-Container WordPress-alkalmazás](tutorial-multi-container-app.md)
::: zone-end

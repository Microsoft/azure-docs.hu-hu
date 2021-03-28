---
title: CI/CD – egyéni tárolók
description: Állítsa be a folyamatos üzembe helyezést egy egyéni Windows-vagy Linux-tárolóra Azure App Serviceban.
keywords: Azure app Service, Linux, Docker, ACR, OSS
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 03/12/2021
ms.author: msangapu
ms.custom: seodec18
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 654b0f842a3165926242d1ef03f2dfe4e5bacfdc
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643350"
---
# <a name="continuous-deployment-with-custom-containers-in-azure-app-service"></a>Folyamatos üzembe helyezés egyéni tárolókkal Azure App Service

Ebben az oktatóanyagban a folyamatos üzembe helyezést konfigurálja a felügyelt [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) adattárakból vagy a [Docker hub](https://hub.docker.com)-ból származó egyéni tároló-rendszerképhez.

## <a name="1-go-to-deployment-center"></a>1. Nyissa meg a központi telepítési központot

A [Azure Portal](https://portal.azure.com)navigáljon a app Service alkalmazás felügyeleti lapjára.

A bal oldali menüben kattintson a **központi telepítési központ**  >  **beállításai** elemre. 

::: zone pivot="container-linux"
## <a name="2-choose-deployment-source"></a>2. a központi telepítés forrásának kiválasztása

**Válassza ki** a telepítési forrást a forgatókönyvtől függően:
- A **Container Registry** BEÁLLÍTJA a CI/CD-t a tároló-beállításjegyzék és a app Service között.
- Ha a githubon tartja karban a tároló képét, akkor a **GitHub-műveletek** beállítással megtarthatja a forráskódot. Új véglegesíti a GitHub-tárházban, az üzembe helyezési művelet futtatható `docker build` és `docker push` közvetlenül a tároló-beállításjegyzékbe, majd az új rendszerkép futtatásához frissítse a app Service alkalmazást. További információ: [how CI/CD a GitHub-műveletekkel működik](#how-cicd-works-with-github-actions).
- A CI/CD **Azure-folyamatokkal** való beállításával kapcsolatban lásd: [Azure webalkalmazás-tároló üzembe helyezése az Azure-folyamatokból](/azure/devops/pipelines/targets/webapp-on-container-linux).

> [!NOTE]
> Docker-összeállítási alkalmazás esetén válassza a **Container Registry** lehetőséget.

Ha a GitHub-műveletek lehetőséget választja, kattintson az **Engedélyezés** **elemre** , és kövesse az engedélyezési utasításokat. Ha korábban már engedélyezte a GitHubot, a **fiók módosítása** lehetőségre kattintva telepítheti egy másik felhasználó adattárát.

Miután engedélyezte az Azure-fiókját a GitHubon, **válassza ki azt** a **szervezetet**, **tárat** és **ágat** , amelyet üzembe szeretne helyezni.
::: zone-end  

::: zone pivot="container-windows"
## <a name="2-configure-registry-settings"></a>2. a beállításjegyzék beállításainak konfigurálása
::: zone-end  
::: zone pivot="container-linux"
## <a name="3-configure-registry-settings"></a>3. a beállításjegyzék beállításainak konfigurálása

Többtárolós (Docker-összeállítási) alkalmazás üzembe helyezéséhez válassza a **Docker-összeállítás** **elemet** a **tároló típusa mezőben**.

Ha nem látja a **tároló típusa** legördülő menüt, görgessen vissza a **forrásra** , és válassza a **Container Registry** **lehetőséget** .
::: zone-end

A **beállításjegyzék forrása** területen **válassza ki** a tároló beállításjegyzékének helyét. Ha nem Azure Container Registry sem a Docker hub, válassza a **privát beállításjegyzék** **lehetőséget** .

::: zone pivot="container-linux"
> [!NOTE]
> Ha a többszörös tároló (Docker-összeállítás) alkalmazás több privát lemezképet használ, győződjön meg arról, hogy a privát lemezképek ugyanabban a privát beállításjegyzékben találhatók, és ugyanazokkal a felhasználói hitelesítő adatokkal lesznek elérhetők. Ha a többtárolós alkalmazás csak nyilvános képeket használ, **válassza** a **Docker hub** lehetőséget, még akkor is, ha néhány rendszerkép nem található a Docker hub-ban.
::: zone-end  

Kövesse a következő lépéseket úgy, hogy kiválasztja a választott lapot.

# <a name="azure-container-registry"></a>[Azure Container Registry](#tab/acr)

A **beállításjegyzék** legördülő listájában az alkalmazással megegyező előfizetésben szereplő kibocsátásiegység-forgalmi jegyzékek jelennek meg. **Válassza ki** a kívánt beállításjegyzéket.

> [!NOTE]
> Ha egy másik előfizetésben lévő beállításjegyzékből kíván üzembe helyezni, válassza a **privát beállításjegyzék** **lehetőséget** a **beállításjegyzékben** .

::: zone pivot="container-windows"
**Válassza ki** a telepítendő **lemezképet** és **címkét** . Ha szeretné, **írja be** a Startup (indítás) parancsot az **indítási fájlban**. 
::: zone-end
::: zone pivot="container-linux"
Kövesse a következő lépést a **tároló típusától** függően:
- A **Docker-összeállításnál** **válassza ki** a saját lemezképek beállításjegyzékét. **Kattintson a** **fájl kiválasztása** gombra a [Docker-összeállítás fájljának](https://docs.docker.com/compose/compose-file/)feltöltéséhez, vagy csak **illessze** be a Docker-összeállítási fájl tartalmát a **config**-ba.
- **Egyetlen tároló** esetén **válassza ki** a telepíteni kívánt **lemezképet** és **címkét** . Ha szeretné, **írja be** a Startup (indítás) parancsot az **indítási fájlban**. 
::: zone-end

App Service hozzáfűzi a karakterláncot az **indítási fájlban** a [parancs végéhez `docker run` ( `[COMMAND] [ARG...]` szegmensként)](https://docs.docker.com/engine/reference/run/) a tároló indításakor.

# <a name="docker-hub"></a>[Docker Hub](#tab/dockerhub)

::: zone pivot="container-windows"
A **tárház-hozzáférés** területen **válassza ki** , hogy a telepítendő lemezkép nyilvános vagy privát.
::: zone-end
::: zone pivot="container-linux"
A **tárház-hozzáférés** területen **válassza ki** , hogy a telepítendő lemezkép nyilvános vagy privát. Egy vagy több privát képpel rendelkező Docker-összeállításhoz válassza a  **magánjellegű** lehetőséget.
::: zone-end

Ha privát képet választ, **adja meg** a Docker-fiók **bejelentkezési** nevét és **jelszavát** .

::: zone pivot="container-windows"
**Adja** meg a képet és a címkét a **teljes rendszerkép nevében és a címkében**, vesszővel elválasztva `:` (például: `nginx:latest` ). Ha szeretné, **írja be** a Startup (indítás) parancsot az **indítási fájlban**. 
::: zone-end
::: zone pivot="container-linux"
Kövesse a következő lépést a **tároló típusától** függően:
- A **Docker-összeállításnál** **válassza ki** a saját lemezképek beállításjegyzékét. **Kattintson a** **fájl kiválasztása** gombra a [Docker-összeállítás fájljának](https://docs.docker.com/compose/compose-file/)feltöltéséhez, vagy csak **illessze** be a Docker-összeállítási fájl tartalmát a **config**-ba.
- **Egyetlen tároló** esetén **adja** meg a képet és a címkét a **teljes rendszerkép neve és címkéje** mezőben, vesszővel elválasztva `:` (például: `nginx:latest` ). Ha szeretné, **írja be** a Startup (indítás) parancsot az **indítási fájlban**. 
::: zone-end

App Service hozzáfűzi a karakterláncot az **indítási fájlban** a [parancs végéhez `docker run` ( `[COMMAND] [ARG...]` szegmensként)](https://docs.docker.com/engine/reference/run/) a tároló indításakor.

# <a name="private-registry"></a>[Privát beállításjegyzék](#tab/private)

A **kiszolgáló URL-címe** mezőbe **írja be** a kiszolgáló url-címét a **https://**-vel kezdődően.

A **login (bejelentkezés**) és a **Password (jelszó** ) **mezőbe írja be** a privát beállításjegyzék bejelentkezési hitelesítő adatait.

::: zone pivot="container-windows"
**Adja** meg a képet és a címkét a **teljes rendszerkép nevében és a címkében**, vesszővel elválasztva `:` (például: `nginx:latest` ). Ha szeretné, **írja be** a Startup (indítás) parancsot az **indítási fájlban**. 
::: zone-end
::: zone pivot="container-linux"
Kövesse a következő lépést a **tároló típusától** függően:
- A **Docker-összeállításnál** **válassza ki** a saját lemezképek beállításjegyzékét. **Kattintson a** **fájl kiválasztása** gombra a [Docker-összeállítás fájljának](https://docs.docker.com/compose/compose-file/)feltöltéséhez, vagy csak **illessze** be a Docker-összeállítási fájl tartalmát a **config**-ba.
- **Egyetlen tároló** esetén **adja** meg a képet és a címkét a **teljes rendszerkép neve és címkéje** mezőben, vesszővel elválasztva `:` (például: `nginx:latest` ). Ha szeretné, **írja be** a Startup (indítás) parancsot az **indítási fájlban**. 
::: zone-end

App Service hozzáfűzi a karakterláncot az **indítási fájlban** a [parancs végéhez `docker run` ( `[COMMAND] [ARG...]` szegmensként)](https://docs.docker.com/engine/reference/run/) a tároló indításakor.

-----

::: zone pivot="container-windows"
## <a name="3-enable-cicd"></a>3. a CI/CD engedélyezése
::: zone-end
::: zone pivot="container-linux"
## <a name="4-enable-cicd"></a>4. a CI/CD engedélyezése
::: zone-end

App Service támogatja a CI/CD-integrációt a Azure Container Registry és a Docker hub szolgáltatással. Az engedélyezéshez válassza a be **lehetőséget** **a** **folyamatos üzembe helyezés** lehetőségnél.

::: zone pivot="container-linux"
> [!NOTE]
> Ha kijelöli a **GitHub-műveletek** a **forrásban** lehetőséget, akkor ez a beállítás nem érhető el, mert a CI/CD-t közvetlenül a GitHub-műveletek kezelik. Ehelyett a **munkafolyamat konfigurációs** szakasza jelenik meg, ahol a **fájl előnézete** **elemre kattintva** ellenőrizheti a munkafolyamat-fájlt. Az Azure a kiépítési és üzembe helyezési feladatok kezeléséhez véglegesíti ezt a fájlt a kiválasztott GitHub-tárházban. További információ: [how CI/CD a GitHub-műveletekkel működik](#how-cicd-works-with-github-actions).
::: zone-end

Ha engedélyezi ezt a beállítást, App Service hozzáadja a webhookot a tárházhoz Azure Container Registry vagy Docker hub-ban. A tárház azon bejegyzései, amelyek a kiválasztott rendszerképnek a használatával való frissítésekor frissülnek `docker push` . A webhook a App Service alkalmazás újraindítását és futtatását okozza `docker pull` a frissített rendszerkép beszerzéséhez.

**Más privát** beállításjegyzékek esetén manuálisan is közzéteheti a webhookot, vagy megadhatja a CI/CD folyamat lépéseit. A **webhook URL-címében** **kattintson** a **Másolás** gombra a webhook URL-címének lekéréséhez.

::: zone pivot="container-linux"
> [!NOTE]
> A több tárolós (Docker-összeállítási) alkalmazások támogatása korlátozott: 
> - Azure Container Registry esetén a App Service egy webhookot hoz létre a kiválasztott beállításjegyzékben a hatókörként megadott beállításjegyzékben. A a `docker push` beállításjegyzékben lévő bármely adattár (beleértve a Docker-összeállítási fájl által nem hivatkozott fájlokat is) elindítja az alkalmazások újraindítását. Előfordulhat, hogy [a webhookot](../container-registry/container-registry-webhook.md) szűkebb hatókörre szeretné módosítani.
> - A Docker hub a beállításjegyzék szintjén nem támogatja a webhookok használatát. A webhookokat manuálisan kell **hozzáadnia** a Docker-összeállítási fájlban megadott képekhez.
::: zone-end

::: zone pivot="container-windows"
## <a name="4-save-your-settings"></a>4. a beállítások mentése
::: zone-end
::: zone pivot="container-linux"
## <a name="5-save-your-settings"></a>5. a beállítások mentése
::: zone-end

**Kattintson** a **Mentés** gombra.

::: zone pivot="container-linux"

## <a name="how-cicd-works-with-github-actions"></a>A CI/CD működése a GitHub-műveletekkel

Ha GitHub- **műveleteket** választ a **forrásban** (lásd: [telepítési forrás kiválasztása](#2-choose-deployment-source)), app Service beállítja a CI/CD-t az alábbi módokon:

- Egy GitHub-művelet munkafolyamati fájljának a GitHub-adattárba való beléptetésével kezeli a felépítési és üzembe helyezési feladatokat a App Service.
- A GitHub-titokként hozzáadja a saját beállításjegyzékhez tartozó hitelesítő adatokat. A létrehozott munkafolyamat-fájl az [Azure/Docker-login](https://github.com/Azure/docker-login) műveletet futtatja a privát beállításjegyzékbe való bejelentkezéshez, majd a futtatásával `docker push` telepíti azt.
- Hozzáadja az alkalmazás közzétételi profilját GitHub-titokként. A generált munkafolyamat-fájl ezt a titkot használja a App Service való hitelesítéshez, majd futtatja az [Azure/webapps-Deploy](https://github.com/Azure/webapps-deploy) műveletet a frissített lemezkép konfigurálásához, amely elindítja az alkalmazás újraindítását a frissített rendszerkép lekéréséhez.
- Rögzíti az adatokat a [munkafolyamat-futtatási naplókból](https://docs.github.com/actions/managing-workflow-runs/using-workflow-run-logs) , és megjeleníti az alkalmazás **központi telepítési központ** **naplók** lapján.

A GitHub-műveletek Build szolgáltatóját a következő módokon szabhatja testre:

- A munkafolyamat-fájl testreszabása a GitHub-tárházban való létrehozása után. További információ: [a GitHub-műveletek munkafolyamat-szintaxisa](https://docs.github.com/actions/reference/workflow-syntax-for-github-actions). Győződjön meg arról, hogy a munkafolyamat az [Azure/webapps-Deploy](https://github.com/Azure/webapps-deploy) művelettel végződik, és elindítja az alkalmazások újraindítását.
- Ha a kiválasztott ág védett, akkor a konfiguráció mentése nélkül is megtekintheti a munkafolyamat-fájlt, majd manuálisan is hozzáadhatja a szükséges GitHub-titkokat a tárházhoz. Ez a metódus nem biztosítja a naplózási integrációt a Azure Portal.
- A közzétételi profil helyett telepítsen egy [egyszerű szolgáltatásnevet](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) a Azure Active Directory-ben.

#### <a name="authenticate-with-a-service-principal"></a>Hitelesítés egy egyszerű szolgáltatással

Ez a választható konfiguráció helyettesíti az alapértelmezett hitelesítést a közzétételi profilokkal a generált munkafolyamat-fájlban.

Hozzon **létre** egy egyszerű szolgáltatást az az [ad SP Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) paranccsal az [Azure CLI](/cli/azure/)-ben. A következő példában cserélje le a *\<subscription-id>* , a *\<group-name>* és a *\<app-name>* értéket a saját értékeire. **Mentse** a következő lépéshez a teljes JSON-kimenetet, beleértve a legfelső szintűt is `{}` .

```azurecli-interactive
az ad sp create-for-rbac --name "myAppDeployAuth" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

> [!IMPORTANT]
> A biztonság érdekében adja meg az egyszerű szolgáltatáshoz szükséges minimális hozzáférést. Az előző példában szereplő hatókör az adott App Service alkalmazásra korlátozódik, nem a teljes erőforráscsoporthoz.

A [githubon](https://github.com/) **Keresse** meg az adattárat, majd válassza a **Beállítások > titkok > új titok hozzáadása** **lehetőséget** . **Illessze be** a teljes JSON-kimenetet az Azure CLI-parancsból a titok érték mezőjébe. **Adjon** egy nevet a titoknak `AZURE_CREDENTIALS` .

A **központi telepítési központ** által létrehozott munkafolyamat-fájlban **módosítsa** a lépést a `azure/webapps-deploy` következő példához hasonló kóddal:

```yaml
- name: Sign in to Azure 
# Use the GitHub secret you added
- uses: azure/login@v1
    with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
- name: Deploy to Azure Web App
# Remove publish-profile
- uses: azure/webapps-deploy@v2
    with:
    app-name: '<app-name>'
    slot-name: 'production'
    images: '<registry-server>/${{ secrets.AzureAppService_ContainerUsername_... }}/<image>:${{ github.sha }}'
    - name: Sign out of Azure
    run: |
    az logout
```

::: zone-end

## <a name="automate-with-cli"></a>Automatizálás a parancssori felülettel

A tároló-beállításjegyzék és a Docker-rendszerkép konfigurálásához futtassa az [az WebApp config Container set](/cli/azure/webapp/config/container#az-webapp-config-container-set) **parancsot** .

# <a name="azure-container-registry"></a>[Azure Container Registry](#tab/acr)

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name '<image>:<tag>' --docker-registry-server-url 'https://<registry-name>.azurecr.io' --docker-registry-server-user '<username>' --docker-registry-server-password '<password>'
```

# <a name="docker-hub"></a>[Docker Hub](#tab/dockerhub)

```azurecli-interactive
# Public image
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name>

# Private image
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

# <a name="private-registry"></a>[Privát beállításjegyzék](#tab/private)

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name '<image>:<tag>' --docker-registry-server-url <private-repo-url> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

-----

::: zone pivot="container-linux"
Egy többtárolós (Docker-összeállítási) alkalmazás konfigurálásához **készítse elő** a Docker-fájl helyi összeállítását, majd futtassa az az [WebApp config Container set](/cli/azure/webapp/config/container#az-webapp-config-container-set) **parancsot** a `--multicontainer-config-file` paraméterrel. Ha a Docker-összeállítás fájlja privát képeket tartalmaz, **adja hozzá** `--docker-registry-server-*` a paramétereket az előző példában látható módon.

```azurecli-interactive
az webapp config container set --resource-group <group-name> --name <app-name> --multicontainer-config-file <docker-compose-file>
```
::: zone-end

Ha a CI/CD-t a tároló-beállításjegyzékből az alkalmazásra szeretné konfigurálni, **futtassa** az az [WebApp Deployment Container config](/cli/azure/webapp/deployment/container#az-webapp-deployment-container-config) parancsot a `--enable-cd` paraméterrel. A parancs kimenete a webhook URL-címe, de a webhookot manuálisan kell létrehoznia a beállításjegyzékben egy külön lépésben. Az alábbi példa engedélyezi a CI/CD-t az alkalmazásban, majd a kimenetben a webhook URL-címét használja a Azure Container Registry webhook létrehozásához.

```azurecli-interactive
ci_cd_url=$(az webapp deployment container config --name <app-name> --resource-group <group-name> --enable-cd true --query CI_CD_URL --output tsv)

az acr webhook create --name <webhook-name> --registry <registry-name> --resource-group <group-name> --actions push --uri $ci_cd_url --scope '<image>:<tag>'
```

## <a name="more-resources"></a>További erőforrások

* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [.NET Core-webalkalmazás létrehozása Linuxon futó App Service-ben](quickstart-dotnetcore.md)
* [Gyors útmutató: egyéni tároló futtatása App Serviceon](quickstart-custom-container.md)
* [App Service Linuxon – gyakori kérdések](faq-app-service-linux.md)
* [Egyéni tárolók konfigurálása](configure-custom-container.md)
* [Az Azure-ba telepítendő műveletek munkafolyamatai](https://github.com/Azure/actions-workflow-samples)

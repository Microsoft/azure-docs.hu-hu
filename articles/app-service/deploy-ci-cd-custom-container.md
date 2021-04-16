---
title: CI/CD–egyéni tárolók
description: Folyamatos üzembe helyezés beállítása egyéni Windows- vagy Linux-tárolóra a Azure App Service.
keywords: azure app service, linux, docker, acr,oss
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 03/12/2021
ms.author: msangapu
ms.custom: seodec18, devx-track-azurecli
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: f02aa9fc1bd31bdde6214ab906136a2cac8f1772
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478309"
---
# <a name="continuous-deployment-with-custom-containers-in-azure-app-service"></a>Folyamatos üzembe helyezés egyéni tárolók Azure App Service

Ebben az oktatóanyagban Azure Container Registry felügyelt adattárakból vagy tárolókból származó egyéni [tároló rendszerképének folyamatos](https://azure.microsoft.com/services/container-registry/) üzembe [helyezését](https://hub.docker.com)Docker Hub.

## <a name="1-go-to-deployment-center"></a>1. Ugrás az Üzembe helyezési központra

A [Azure Portal](https://portal.azure.com)nyissa meg a felügyeleti lapot a App Service alkalmazáshoz.

A bal oldali menüben kattintson a **Központi telepítési központ beállításai**  >  **elemre.** 

::: zone pivot="container-linux"
## <a name="2-choose-deployment-source"></a>2. Az üzembe helyezés forrásának kiválasztása

**Az** üzembe helyezési forrás kiválasztása a forgatókönyvtől függ:
- **A Container Registry** beállítja a CI/CD-t a tároló-beállításjegyzék és a App Service.
- A **GitHub Actions** akkor érhető el, ha a GitHubon tartja karban a tároló rendszerképének forráskódját. A GitHub-adattárban való új véglegesítések aktiválják, hogy az üzembe helyezési művelet közvetlenül a tároló-beállításjegyzékben fusson, majd frissítse az App Service-alkalmazást az új `docker build` `docker push` rendszerkép futtatásához. További információ: [How CI/CD works with GitHub Actions](#how-cicd-works-with-github-actions).
- A CI/CD **Azure Pipelines** használatával történő beállításával kapcsolatban lásd: [Azure-webalkalmazás-tároló üzembe helyezése az Azure Pipelinesból.](/azure/devops/pipelines/targets/webapp-on-container-linux)

> [!NOTE]
> Docker Compose-alkalmazáshoz válassza a **Container Registry.**

Ha a hitelesítést GitHub Actions, kattintson **az Engedélyezés** **gombra,** és kövesse az engedélyezési utasításokat. Ha korábban már engedélyezte a GitHubot, egy másik felhasználó adattárában is üzembe helyezheti az alkalmazást a **Fiók módosítása gombra kattintva.**

Miután engedélyezte Azure-fiókját a  GitHubbal, válassza ki azt a **szervezetet,** adattárat és **ágat,** amelyből az üzembe helyezést el kell helyezni.
::: zone-end  

::: zone pivot="container-windows"
## <a name="2-configure-registry-settings"></a>2. Beállításjegyzék-beállítások konfigurálása
::: zone-end  
::: zone pivot="container-linux"
## <a name="3-configure-registry-settings"></a>3. Beállításjegyzék-beállítások konfigurálása

Többtárolós (Docker Compose-) alkalmazás üzembe helyezéséhez **válassza** a **Docker Compose** lehetőséget a **Tárolótípus mezőben.**

Ha nem látja a **Tároló** típusa legördülő menüt, görgessen vissza a **Forráshoz,** és válassza a **Container Registry.** 
::: zone-end

A **Beállításjegyzék-forrás mezőben** **válassza ki,** hol található a tároló-beállításjegyzék. Ha nem ez a Azure Container Registry és **Docker Hub,** válassza a **Privát beállításjegyzék lehetőséget.**

::: zone pivot="container-linux"
> [!NOTE]
> Ha a többtárolós (Docker Compose)-alkalmazás több privát rendszerképet használ, győződjön meg arról, hogy a privát rendszerképek ugyanabban a privát regisztrációs adatbázisban vannak, és ugyanazokkal a felhasználói hitelesítő adatokkal érhetők el. Ha a többtárolós alkalmazás csak nyilvános rendszerképeket **használ,** válassza a **Docker Hub** lehetőséget, még akkor is, ha egyes lemezképek nincsenek a Docker Hub.
::: zone-end  

Kövesse a következő lépéseket a választott lap kiválasztásával.

# <a name="azure-container-registry"></a>[Azure Container Registry](#tab/acr)

A **Beállításjegyzék** legördülő menüben az alkalmazással azonos előfizetésben megjelenik a beállításjegyzék. **Válassza ki** a kívánt beállításjegyzéket.

> [!NOTE]
> Ha egy másik előfizetésben lévő beállításjegyzékből kell üzembe **helyeznie,** válassza inkább a **Privát** beállításjegyzék lehetőséget a **Beállításjegyzék forrásában.**

::: zone pivot="container-windows"
**Válassza ki** **az üzembe** helyezni **kívánt rendszerképet** és címkét. Ha szeretné, írja **be az** indítási parancsot az **Indítási fájl mezőbe.** 
::: zone-end
::: zone pivot="container-linux"
Kövesse a következő lépést a tároló **típusától függően:**
- A **Docker Compose esetén** válassza ki **a** privát rendszerképek beállításjegyzékét. **Kattintson** **a Choose file** (Fájl kiválasztása)  elemre a [Docker Compose-fájl feltöltésére,](https://docs.docker.com/compose/compose-file/)vagy egyszerűen illessze be a Docker Compose-fájl tartalmát a **Config fájlba.**
- Egyetlen **tárolóhoz válassza** **ki az** üzembe helyezni kívánt **rendszerképet** **és** címkét. Ha szeretné, írja **be az** indítási parancsot az **Indítási fájl mezőbe.** 
::: zone-end

App Service indítási fájlban található  sztringet a parancs véghez [ `docker run` fűzi (szegmensként) `[COMMAND] [ARG...]` ](https://docs.docker.com/engine/reference/run/) a tároló indításakor.

# <a name="docker-hub"></a>[Docker Hub](#tab/dockerhub)

::: zone pivot="container-windows"
Az **Adattár-hozzáférés beállításnál** válassza **ki,** hogy az üzembe helyezni kívánt rendszerkép nyilvános vagy privát.
::: zone-end
::: zone pivot="container-linux"
Az **Adattár-hozzáférés beállításnál** válassza **ki,** hogy az üzembe helyezni kívánt rendszerkép nyilvános vagy privát. Egy vagy több privát rendszerképet is tartalmazó Docker Compose-alkalmazás esetén válassza a **Privát** **lehetőséget.**
::: zone-end

Ha privát rendszerképet választ, **adja meg** a Docker-fiók **bejelentkezési** nevét (felhasználónevét) és jelszavát. 

::: zone pivot="container-windows"
**A** kép és a címke nevét a Teljes rendszerkép neve és **Címke** alatt kell megadhatja, egy (például : `:` ) vesszővel elválasztva. `nginx:latest` Ha szeretné, írja **be az** indítási parancsot az **Indítási fájl mezőbe.** 
::: zone-end
::: zone pivot="container-linux"
Kövesse a következő lépést a tároló **típusától függően:**
- A **Docker Compose esetén** válassza ki **a** privát rendszerképek beállításjegyzékét. **Kattintson** **a Choose file** (Fájl kiválasztása)  elemre a [Docker Compose-fájl feltöltésére,](https://docs.docker.com/compose/compose-file/)vagy egyszerűen illessze be a Docker Compose-fájl tartalmát a **Config fájlba.**
- A Single Container (Egytárolós) számára **a** Teljes rendszerkép neve és Címke alatt a rendszerkép és a címke nevét kell megadhatja, egy vesszővel   `:` elválasztva (például `nginx:latest` ). Ha szeretné, írja **be az** indítási parancsot az **Indítási fájl mezőbe.** 
::: zone-end

App Service az indítási fájlban  található sztringet a parancs végére [ `docker run` (szegmensként) `[COMMAND] [ARG...]` ](https://docs.docker.com/engine/reference/run/) fűzi hozzá a tároló indításakor.

# <a name="private-registry"></a>[Privát beállításjegyzék](#tab/private)

A **Kiszolgáló URL-címe** **mezőbe írja be** a kiszolgáló URL-címét a **következővel https://.**

A **Login (Bejelentkezés)** és **a Password (Jelszó)**  mezőbe írja be a privát regisztrációs adatbázis bejelentkezési hitelesítő adatait.

::: zone pivot="container-windows"
**A** kép és a címke nevét a Teljes rendszerkép neve és **Címke** alatt kell megadhatja, egy (például : `:` ) vesszővel elválasztva. `nginx:latest` Ha szeretné, írja **be az** indítási parancsot az **Indítási fájl mezőbe.** 
::: zone-end
::: zone pivot="container-linux"
Kövesse a következő lépést a tároló **típusától függően:**
- A **Docker Compose esetén** válassza ki **a** privát rendszerképek beállításjegyzékét. **Kattintson** **a Choose file** (Fájl kiválasztása)  elemre a [Docker Compose-fájl feltöltésére,](https://docs.docker.com/compose/compose-file/)vagy egyszerűen illessze be a Docker Compose-fájl tartalmát a **Config fájlba.**
- A **Single Container (Egytárolós)** tárolókhoz **a** teljes rendszerképnévben és a **Címkében** a rendszerkép és a címke nevét (például : ) kell `:` elválasztani `nginx:latest` egymástól. Ha szeretné, írja **be az** indítási parancsot az **Indítási fájl mezőbe.** 
::: zone-end

App Service indítási fájlban található  sztringet a parancs véghez [ `docker run` fűzi (szegmensként) `[COMMAND] [ARG...]` ](https://docs.docker.com/engine/reference/run/) a tároló indításakor.

-----

::: zone pivot="container-windows"
## <a name="3-enable-cicd"></a>3. A CI/CD engedélyezése
::: zone-end
::: zone pivot="container-linux"
## <a name="4-enable-cicd"></a>4. A CI/CD engedélyezése
::: zone-end

App Service támogatja a CI/CD-integrációt a Azure Container Registry és Docker Hub. Az engedélyezéséhez válassza **a Be lehetőséget** **a** Folyamatos üzembe **helyezés beállításban.**

::: zone pivot="container-linux"
> [!NOTE]
> Ha a Forrás **GitHub Actions** választja, ez a lehetőség nem lesz meg, mert a CI/CD-t közvetlenül a GitHub Actions kezeli. Ehelyett megjelenik egy **Munkafolyamat-konfiguráció**  szakasz, ahol a Preview file **(Előnézeti** fájl) elemre kattintva megvizsgálhatja a munkafolyamat-fájlt. Az Azure véglegesít egy fájlt a kiválasztott GitHub-forrástárházban a buildfeladatok kezeléséhez és üzembe helyezéséhez. További információ: [How CI/CD works with GitHub Actions](#how-cicd-works-with-github-actions).
::: zone-end

Ha engedélyezi ezt a beállítást, a App Service hozzáad egy webhookot az adattárhoz a Azure Container Registry vagy Docker Hub. Az adattár akkor bejegyzéseket hoz létre ebben a webhookban, ha a kiválasztott rendszerkép frissül a néhával. `docker push` A webhook hatására az App Service az alkalmazás újraindul, majd lefut a frissített `docker pull` rendszerkép lefutása miatt.

**Más privát beállításregisztrálók esetén** a a webhookba manuálisan vagy egy CI/CD-folyamat lépéseként is közzétevődhet. A **Webhook URL-címe** mezőben **kattintson a** Másolás **gombra** a webhook URL-címének lekért gombra.

::: zone pivot="container-linux"
> [!NOTE]
> A többtárolós (Docker Compose-) alkalmazások támogatása korlátozott: 
> - A Azure Container Registry a App Service létrehoz egy webhookot a kiválasztott beállításjegyzékben, hatókörként pedig a beállításjegyzéket. A a beállításjegyzék bármely adattárára (beleértve azokat is, amelyekre a Docker Compose-fájl nem `docker push` hivatkozik) elindítja az alkalmazás újraindítását. Előfordulhat, hogy [a webhookot szűkebb](../container-registry/container-registry-webhook.md) hatókörre szeretné módosítani.
> - Docker Hub a beállításjegyzék szintjén nem támogatja a webhookokat. A  webhookokat manuálisan kell hozzáadnia a Docker Compose-fájlban megadott rendszerképekhez.
::: zone-end

::: zone pivot="container-windows"
## <a name="4-save-your-settings"></a>4. A beállítások mentése
::: zone-end
::: zone pivot="container-linux"
## <a name="5-save-your-settings"></a>5. A beállítások mentése
::: zone-end

**Kattintson a** **Mentés gombra.**

::: zone pivot="container-linux"

## <a name="how-cicd-works-with-github-actions"></a>A CI/CD és a GitHub Actions

Ha a Forrás **GitHub Actions** **választja** (lásd: [Telepítési](#2-choose-deployment-source)forrás kiválasztása), App Service a CI/CD-t a következő módokon állítja be:

- Egy munkafolyamatfájlt GitHub Actions a GitHub-adattárba, hogy kezelni tudja az összeállítási és üzembe helyezési App Service.
- GitHub-titkos kódként hozzáadja a privát regisztrációs adatbázis hitelesítő adatait. A létrehozott munkafolyamat-fájl futtatja az [Azure/docker-login](https://github.com/Azure/docker-login) műveletet a privát beállításjegyzékbe való bejelentkezéshez, majd futtatja az üzembe `docker push` helyezéshez.
- Hozzáadja az alkalmazás közzétételi profilját GitHub-titokként. A létrehozott munkafolyamat-fájl ezt a titkos adatokat használja a App Service-hitelesítéshez, majd futtatja az [Azure/webapps-deploy](https://github.com/Azure/webapps-deploy) műveletet a frissített rendszerkép konfigurálása érdekében, amely elindítja az alkalmazás újraindítását a frissített rendszerkép le kérhetőként.
- Rögzíti a munkafolyamat-futtatási [naplók](https://docs.github.com/actions/managing-workflow-runs/using-workflow-run-logs) adatait, és megjeleníti azokat az alkalmazás Deployment  **Centerének Naplók lapján.**

Az alkalmazás-GitHub Actions a következő módokon szabhatja testre:

- A Munkafolyamat-fájl testreszabása a GitHub-adattárban való létrehozása után. További információ: [Munkafolyamat-szintaxis GitHub Actions.](https://docs.github.com/actions/reference/workflow-syntax-for-github-actions) Csak győződjön meg arról, hogy a munkafolyamat [az Azure/webapps-deploy műveletre](https://github.com/Azure/webapps-deploy) végződik, hogy elindítsa az alkalmazás újraindítását.
- Ha a kiválasztott ág védett, akkor is megtekintheti a munkafolyamat-fájl előnézetét a konfiguráció mentése nélkül, majd manuálisan hozzáadhatja azt és a szükséges GitHub-titkos kódokat az adattárhoz. Ezzel a módszerrel nem integrálja a naplót a Azure Portal.
- Közzétételi profil helyett az üzembe helyezést [szolgáltatásnév](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) használatával kell Azure Active Directory.

#### <a name="authenticate-with-a-service-principal"></a>Hitelesítés szolgáltatásnévvel

Ez a választható konfiguráció lecseréli az alapértelmezett hitelesítést a létrehozott munkafolyamat-fájlban található közzétételi profilokra.

**Hozzon** létre egy egyszerű szolgáltatást [az az ad sp create-for-rbac paranccsal](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) az [Azure CLI-n.](/cli/azure/) A következő példában cserélje le a *\<subscription-id>* , *\<group-name>* és értékeket a saját *\<app-name>* értékeire. **Mentse** a teljes JSON-kimenetet a következő lépéshez, beleértve a legfelső szintű kimenetet `{}` is.

```azurecli-interactive
az ad sp create-for-rbac --name "myAppDeployAuth" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

> [!IMPORTANT]
> A biztonság érdekében adja meg a minimálisan szükséges hozzáférést a szolgáltatásnévhez. Az előző példában a hatókör nem a teljes erőforráscsoportra, App Service alkalmazásra van korlátozva.

A [GitHubon](https://github.com/)keresse **meg** az adattárat, majd válassza a Settings > Secrets > Add a new secret (Új titkos kód  **hozzáadása) lehetőséget.** **Illessze** be az Azure CLI-parancs teljes JSON-kimenetét a titkos gombra. **Adjon nevet** a titkos nak, `AZURE_CREDENTIALS` például: .

Az Üzembe helyezési központ által létrehozott munkafolyamat-fájlban **módosítsa** a lépést az alábbi példához hasonló `azure/webapps-deploy` kóddal:

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

## <a name="automate-with-cli"></a>Automatizálás a CLI-val

A tároló-beállításjegyzék és a Docker-rendszerkép konfigurálához **futtassa** [az az webapp config container set et.](/cli/azure/webapp/config/container#az-webapp-config-container-set)

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
Többtárolós (Docker Compose)-alkalmazás konfiguráláshoz helyileg készítse  elő **a** Docker Compose-fájlt, majd futtassa az [az webapp config container set](/cli/azure/webapp/config/container#az-webapp-config-container-set) `--multicontainer-config-file` paramétert. Ha a Docker Compose-fájl privát rendszerképeket tartalmaz, adjon **hozzá** `--docker-registry-server-*` paramétereket az előző példában látható módon.

```azurecli-interactive
az webapp config container set --resource-group <group-name> --name <app-name> --multicontainer-config-file <docker-compose-file>
```
::: zone-end

A CI/CD a tároló-beállításjegyzékből az alkalmazásba való konfigurálához **futtassa** [az az webapp deployment container config](/cli/azure/webapp/deployment/container#az-webapp-deployment-container-config) az `--enable-cd` paraméterrel. A parancs kimenete a webhook URL-címe, de a webhookot manuálisan kell létrehoznia a beállításjegyzékben egy külön lépésben. Az alábbi példa engedélyezi a CI/CD-t az alkalmazáson, majd a kimenetben található webhook URL-címével létrehozza a webhookot a Azure Container Registry.

```azurecli-interactive
ci_cd_url=$(az webapp deployment container config --name <app-name> --resource-group <group-name> --enable-cd true --query CI_CD_URL --output tsv)

az acr webhook create --name <webhook-name> --registry <registry-name> --resource-group <group-name> --actions push --uri $ci_cd_url --scope '<image>:<tag>'
```

## <a name="more-resources"></a>További erőforrások

* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [.NET Core-webalkalmazás létrehozása Linuxon futó App Service-ben](quickstart-dotnetcore.md)
* [Rövid útmutató: Egyéni tároló futtatása a App Service](quickstart-custom-container.md)
* [App Service Linuxon – gyakori kérdések](faq-app-service-linux.md)
* [Egyéni tárolók konfigurálása](configure-custom-container.md)
* [Az Azure-ban üzembe helyező műveletek munkafolyamatai](https://github.com/Azure/actions-workflow-samples)

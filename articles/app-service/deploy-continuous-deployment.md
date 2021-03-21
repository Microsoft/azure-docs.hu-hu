---
title: Folyamatos üzembe helyezés konfigurálása
description: Megtudhatja, hogyan engedélyezheti a CI/CD-t a GitHub, a BitBucket, az Azure Repos vagy más repók Azure App Service. Válassza ki az igényeinek megfelelő Build-folyamatot.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 03/12/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 52f0db739cff9614dc4e9f5ef71d582e926fc65a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103470268"
---
# <a name="continuous-deployment-to-azure-app-service"></a> Folyamatos üzembe helyezés az Azure App Service-be

[Azure app Service](overview.md) lehetővé teszi a [GitHub](https://help.github.com/articles/create-a-repo), a [BitBucket](https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html)és az [Azure Repos](/azure/devops/repos/git/creatingrepo) -Tárházak folyamatos üzembe helyezését a legújabb frissítések behúzásával.

> [!NOTE]
> A Azure Portal, amely a régi telepítési élmény, a **fejlesztői központ (klasszikus)** lapja 2021 márciusában elavulttá válik. Ez a módosítás nem befolyásolja az alkalmazásban meglévő központi telepítési beállításokat, és az alkalmazás központi telepítését továbbra is kezelheti a **központi telepítési központ** lapon.

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="configure-deployment-source"></a>Központi telepítési forrás konfigurálása

1. A [Azure Portal](https://portal.azure.com)navigáljon a app Service alkalmazás felügyeleti lapjára.

1. A bal oldali menüben kattintson a **központi telepítési központ**  >  **beállításai** elemre. 

1. A **forrás** területen válassza ki az egyik CI/CD beállítást.

    ![Bemutatja, hogyan választható ki a központi telepítési forrás a központi telepítési központban Azure App Service](media/app-service-continuous-deployment/choose-source.png)

Válassza ki azt a fület, amely megfelel a lépéseknek.

# <a name="github"></a>[GitHub](#tab/github)

4. A [GitHub-műveletek](#how-the-github-actions-build-provider-works) az alapértelmezett Build-szolgáltató. A módosításhoz kattintson a **szolgáltató**  >  **app Service Build Service** (kudu) elemre > **OK gombra**.

    > [!NOTE]
    > Ha az Azure-folyamatokat a App Service-alkalmazáshoz tartozó Build szolgáltatóként szeretné használni, ne konfigurálja App Service. Ehelyett konfigurálja a CI/CD-t közvetlenül az Azure-folyamatokból. Az **Azure-folyamatok** lehetőség csak a megfelelő irányba mutat.

1. Ha első alkalommal telepít üzembe a GitHubról, kattintson az **Engedélyezés** elemre, és kövesse az engedélyezési utasításokat. Ha egy másik felhasználó adattárát szeretné telepíteni, kattintson a **fiók módosítása** gombra.

1. Miután engedélyezte az Azure-fiókját a GitHubon, válassza ki a **szervezetet**, a **tárházat** és az **ágat** a CI/CD konfigurálásához.

1. Ha a GitHub-műveletek a kiválasztott Build-szolgáltató, kiválaszthatja a kívánt munkafolyamat-fájlt a **futásidejű verem** és a **verziók** legördülő lista használatával. Az Azure elvégzi ezt a munkafolyamat-fájlt a kiválasztott GitHub-tárházban a létrehozási és üzembe helyezési feladatok kezeléséhez. Ha meg szeretné tekinteni a fájlt a módosítások mentése előtt, kattintson az **előnézet fájl** elemre.

    > [!NOTE]
    > App Service észleli az alkalmazás [nyelvi verem beállítását](configure-common.md#configure-language-stack-settings) , és kiválasztja a legmegfelelőbb munkafolyamat-sablont. Ha másik sablont választ, előfordulhat, hogy olyan alkalmazást telepít, amely nem megfelelően fut. További információ: [How the GitHub Actions Build Provider működik](#how-the-github-actions-build-provider-works).

1. Kattintson a **Mentés** gombra.
   
    Új véglegesíti a kiválasztott tárházban és ág-ban, most már folyamatosan üzembe helyezi a App Service alkalmazást. A commit és a központi telepítéseket a **naplók** lapon követheti nyomon.

# <a name="bitbucket"></a>[BitBucket](#tab/bitbucket)

A BitBucket-integráció a App Service Build Services (kudu) szolgáltatást használja a Build Automation számára.

4. Ha első alkalommal telepíti a BitBucket-t, kattintson az **Engedélyezés** elemre, és kövesse az engedélyezési utasításokat. Ha egy másik felhasználó adattárát szeretné telepíteni, kattintson a **fiók módosítása** gombra.

1. A bitbucket területen válassza ki azt a bitbucket- **csoportot**, **tárat** és **ágat** , amelyet folyamatosan telepíteni szeretne.

1. Kattintson a **Mentés** gombra.
   
    Új véglegesíti a kiválasztott tárházban és ág-ban, most már folyamatosan üzembe helyezi a App Service alkalmazást. A commit és a központi telepítéseket a **naplók** lapon követheti nyomon.
   
# <a name="local-git"></a>[Helyi Git](#tab/local)

Lásd: [helyi git-telepítés Azure app Service](deploy-local-git.md).

# <a name="azure-repos"></a>[Azure Repos](#tab/repos)

> [!NOTE]
> Az Azure Repos központi telepítési forrása a Windows-alkalmazások támogatása.
>

4. A App Service Build Service (kudu) az alapértelmezett Build-szolgáltató.

    > [!NOTE]
    > Ha az Azure-folyamatokat a App Service-alkalmazáshoz tartozó Build szolgáltatóként szeretné használni, ne konfigurálja App Service. Ehelyett konfigurálja a CI/CD-t közvetlenül az Azure-folyamatokból. Az **Azure-folyamatok** lehetőség csak a megfelelő irányba mutat.

1. Válassza ki azt az **Azure DevOps-szervezetet**, **projektet**, **tárházat** és **ágat** , amelyet folyamatosan szeretne üzembe helyezni. 

    Ha a DevOps-szervezet nem szerepel a listáján, még nem kapcsolódik az Azure-előfizetéséhez. További információ: Azure- [szolgáltatásbeli kapcsolatok létrehozása](/azure/devops/pipelines/library/connect-to-azure).

-----

## <a name="disable-continuous-deployment"></a>Folyamatos üzembe helyezés letiltása

1. A [Azure Portal](https://portal.azure.com)navigáljon a app Service alkalmazás felügyeleti lapjára.

1. A bal oldali menüben kattintson a **központi telepítési központ**  >  **beállításai**  >  **Leválasztás** lehetőségre. 

    ![Bemutatja, hogyan lehet leválasztani a Cloud Folder syncet a App Service alkalmazással a Azure Portal.](media/app-service-continuous-deployment/disable.png)

1. Alapértelmezés szerint a GitHub-műveletek munkafolyamat-fájlja megmarad a tárházban, de továbbra is elindítja az üzembe helyezést az alkalmazásban. Ha törölni szeretné a tárházból, válassza a **munkafolyamat-fájl törlése** lehetőséget.

1. Kattintson az **OK** gombra.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="how-the-github-actions-build-provider-works"></a>A GitHub-műveletek Build-szolgáltatójának működése

A GitHub-műveletek Build Provider a [CI/CD a githubból](#configure-deployment-source)való beállítása, valamint a CI/CD beállításához a következők szükségesek:

- Egy GitHub-művelet munkafolyamati fájljának a GitHub-adattárba való beléptetésével kezeli a felépítési és üzembe helyezési feladatokat a App Service.
- Hozzáadja az alkalmazás közzétételi profilját GitHub-titokként. A munkafolyamat-fájl ezt a titkot használja a App Service való hitelesítéshez.
- Rögzíti az adatokat a [munkafolyamat-futtatási naplókból](https://docs.github.com/actions/managing-workflow-runs/using-workflow-run-logs) , és megjeleníti az alkalmazás **központi telepítési központ** **naplók** lapján.

A GitHub-műveletek Build szolgáltatóját a következő módokon szabhatja testre:

- A munkafolyamat-fájl testreszabása a GitHub-tárházban való létrehozása után. További információ: [a GitHub-műveletek munkafolyamat-szintaxisa](https://docs.github.com/actions/reference/workflow-syntax-for-github-actions). Győződjön meg arról, hogy a munkafolyamat üzembe helyezése az [Azure/webapps-Deploy](https://github.com/Azure/webapps-deploy) művelettel app Service.
- Ha a kiválasztott ág védett, a konfiguráció mentése nélkül is megtekintheti a munkafolyamat-fájlt, majd manuálisan hozzáadhatja azt a tárházhoz. Ez a metódus nem biztosítja a naplózási integrációt a Azure Portal.
- A közzétételi profil helyett telepítsen egy [egyszerű szolgáltatásnevet](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) a Azure Active Directory-ben.

#### <a name="authenticate-with-a-service-principal"></a>Hitelesítés egy egyszerű szolgáltatással

Ez a választható konfiguráció helyettesíti az alapértelmezett hitelesítést a közzétételi profilokkal a generált munkafolyamat-fájlban.

1. Hozzon létre egy egyszerű szolgáltatást az az [ad SP Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) paranccsal az [Azure CLI](/cli/azure/)-ben. A következő példában cserélje le a, a *\<subscription-id>* *\<group-name>* és a értéket a *\<app-name>* saját értékeire:

    ```azurecli-interactive
    az ad sp create-for-rbac --name "myAppDeployAuth" --role contributor \
                                --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                                --sdk-auth
    ```
    
    > [!IMPORTANT]
    > A biztonság érdekében adja meg az egyszerű szolgáltatáshoz szükséges minimális hozzáférést. Az előző példában szereplő hatókör az adott App Service alkalmazásra korlátozódik, nem a teljes erőforráscsoporthoz.
    
1. Mentse a következő lépéshez a teljes JSON-kimenetet, beleértve a legfelső szintűt is `{}` .

1. A [githubon](https://github.com/)tallózzon a tárházban, válassza a **beállítások > titkok > új titok hozzáadása** lehetőséget.

1. Illessze be a teljes JSON-kimenetet az Azure CLI-parancsból a titok érték mezőjébe. Adjon egy nevet a titoknak `AZURE_CREDENTIALS` .

1. A **központi telepítési központ** által létrehozott munkafolyamat-fájlban módosítsa a lépést a `azure/webapps-deploy` következő példához hasonló kóddal (amely Node.js munkafolyamat-fájlból módosul):

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
        package: .
    - name: Sign out of Azure
      run: |
        az logout
    ```
    
## <a name="deploy-from-other-repositories"></a>Üzembe helyezés más adattárakból

Windows-alkalmazások esetén manuálisan is konfigurálhatja a folyamatos üzembe helyezést egy olyan felhőalapú git-vagy Mercurial-adattárból, amelyet a portál közvetlenül nem támogat, például [GitLab](https://gitlab.com/). Ezt úgy teheti meg, hogy kijelöli a külső git elemet a **forrás** legördülő menüben. További információ: a [folyamatos üzembe helyezés beállítása manuális lépésekkel](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

## <a name="more-resources"></a>További erőforrások

* [Üzembe helyezés Azure-folyamatokból az Azure-ba App Services](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps)
* [A folyamatos üzembe helyezéssel kapcsolatos gyakori problémák vizsgálata](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Azure PowerShell használatával](/powershell/azure/)
* [A Kudu projekt](https://github.com/projectkudu/kudu/wiki)

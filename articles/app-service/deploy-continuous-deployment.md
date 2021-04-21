---
title: Folyamatos üzembe helyezés konfigurálása
description: Megtudhatja, hogyan engedélyezheti a CI/CD-Azure App Service GitHubból, a BitBucketből, az Azure Reposból vagy más adattárakból. Válassza ki az igényeinek megfelelő build folyamatot.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 03/12/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 633d62fc69c516b482d5749a07052337dc71f567
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789482"
---
# <a name="continuous-deployment-to-azure-app-service"></a> Folyamatos üzembe helyezés az Azure App Service-be

[Azure App Service](overview.md) [github-,](https://help.github.com/articles/create-a-repo) [BitBucket-](https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html)és Azure [Repos-adattárakból](/azure/devops/repos/git/creatingrepo) a legújabb frissítések lehúzásával teszi lehetővé a folyamatos üzembe helyezést.

> [!NOTE]
> A **fejlesztői központ (klasszikus)** Azure Portal, amely a régi üzembe helyezési folyamat, 2021 márciusában elavult lesz. Ez a módosítás nem érinti az alkalmazás meglévő telepítési beállításait, és az alkalmazástelepítést a Központi telepítési központ **lapon folytathatja.**

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="configure-deployment-source"></a>Központi telepítési forrás konfigurálása

1. A [Azure Portal](https://portal.azure.com)nyissa meg a felügyeleti lapot a App Service alkalmazáshoz.

1. A bal oldali menüben kattintson a **Központi telepítési központ beállításai**  >  **elemre.** 

1. A **Forrás mezőben** válassza ki az egyik CI/CD-lehetőséget.

    ![Bemutatja, hogyan választhatja ki az üzembe helyezési forrást a központi telepítési Azure App Service](media/app-service-continuous-deployment/choose-source.png)

Válassza ki a lépésekhez kiválasztott lapfület.

# <a name="github"></a>[GitHub](#tab/github)

4. [GitHub Actions](#how-the-github-actions-build-provider-works) alapértelmezett buildszolgáltató. A módosításhoz kattintson a **Change provider** App Service Build Service (Kudu) (Szolgáltató módosítása a  >  **Build Service** (Kudu) > OK **gombra.**

    > [!NOTE]
    > Ha az Azure Pipelinest az alkalmazás buildszolgáltatójaként App Service, ne konfigurálja azt a App Service. Ehelyett konfigurálja a CI/CD-t közvetlenül az Azure Pipelinesból. Az **Azure Pipelines lehetőség** csak a megfelelő irányba mutat.

1. Ha először helyez üzembe a GitHubról, kattintson az Engedélyezés gombra, **és** kövesse az engedélyezési utasításokat. Ha egy másik felhasználó adattárában szeretne üzembe helyezést, kattintson a **Fiók módosítása elemre.**

1. Miután engedélyezte azure-fiókját a GitHubbal, válassza ki az **Organization**(Szervezet) , **Repository**(Tárház) és **Branch** (Ág) szolgáltatásokat a CI/CD konfiguráláshoz.

1. Ha GitHub Actions a kiválasztott buildszolgáltató, kiválaszthatja a kívánt munkafolyamat-fájlt a **Futásidejű** verem és a **Verzió** legördülő menüben. Az Azure véglegesít egy munkafolyamat-fájlt a kiválasztott GitHub-adattárban a buildfeladatok kezeléséhez és üzembe helyezéséhez. A módosítások mentése előtt a fájl megtekintéséhez kattintson a Preview file (Fájl **előnézete) elemre.**

    > [!NOTE]
    > App Service észleli az alkalmazás nyelvi verembeállítását, és kiválasztja a legmegfelelőbb munkafolyamat-sablont. [](configure-common.md#configure-language-stack-settings) Ha másik sablont választ, előfordulhat, hogy olyan alkalmazást helyez üzembe, amely nem megfelelően fut. További információ: [A GitHub Actions buildszolgáltató működése.](#how-the-github-actions-build-provider-works)

1. Kattintson a **Mentés** gombra.
   
    A kiválasztott adattárban és ágban az új véglegesítések mostantól folyamatosan üzembe helyezhetők a App Service alkalmazásban. A véglegesítéseket és az üzembe helyezéseket a Naplók lapon **követheti** nyomon.

# <a name="bitbucket"></a>[BitBucket](#tab/bitbucket)

A BitBucket-integráció a App Service Build Services (Kudu) szolgáltatást használja a build automatizálásához.

4. Ha először helyez üzembe a BitBucketből, kattintson az **Engedélyezés** gombra, és kövesse az engedélyezési utasításokat. Ha egy másik felhasználó adattárában szeretne üzembe helyezést, kattintson a **Fiók módosítása elemre.**

1. A Bitbucket mezőben válassza ki a bitbucket  **csapat,** adattár **és** ág folyamatos üzembe helyezését.

1. Kattintson a **Mentés** gombra.
   
    A kiválasztott adattárban és ágban az új véglegesítések mostantól folyamatosan üzembe helyezhetők a App Service alkalmazásban. A véglegesítéseket és az üzembe helyezéseket a Naplók lapon **követheti** nyomon.
   
# <a name="local-git"></a>[Helyi Git](#tab/local)

Lásd: [Local Git deployment to Azure App Service](deploy-local-git.md).

# <a name="azure-repos"></a>[Azure Repos](#tab/repos)

> [!NOTE]
> Az Azure Repos mint üzembe helyezési forrás támogatja a Windows-alkalmazásokat.
>

4. App Service Build Service (Kudu) az alapértelmezett buildszolgáltató.

    > [!NOTE]
    > Ha az Azure Pipelinest az alkalmazás buildszolgáltatójaként App Service, ne konfigurálja a App Service. Ehelyett konfigurálja a CI/CD-t közvetlenül az Azure Pipelinesból. Az **Azure Pipelines lehetőség** csak a megfelelő irányba mutat.

1. Válassza ki a folyamatosan üzembe helyezni kívánt Azure  **DevOps-szervezetet,**  **Projektet,** Adattárat és Ágat. 

    Ha a DevOps-szervezet nem szerepel a listában, még nincs azure-előfizetéséhez kapcsolva. További információ: [Azure-szolgáltatáskapcsolat létrehozása.](/azure/devops/pipelines/library/connect-to-azure)

-----

## <a name="disable-continuous-deployment"></a>Folyamatos üzembe helyezés letiltása

1. A [Azure Portal](https://portal.azure.com)nyissa meg a felügyeleti lapot a App Service alkalmazáshoz.

1. A bal oldali menüben kattintson az **Üzembe helyezési központ beállításai**  >    >  **Leválasztása elemre.** 

    ![Bemutatja, hogyan bonthatja a felhőmappa szinkronizálását a App Service alkalmazással a Azure Portal.](media/app-service-continuous-deployment/disable.png)

1. Alapértelmezés szerint a GitHub Actions munkafolyamat-fájl megőrzve marad az adattárban, de továbbra is elindítja az alkalmazáson belüli üzembe helyezést. Ha törölni szeretné az adattárból, válassza a **Munkafolyamat-fájl törlése lehetőséget.**

1. Kattintson az **OK** gombra.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="how-the-github-actions-build-provider-works"></a>Az GitHub Actions buildszolgáltató működése

A GitHub Actions buildszolgáltató a CI/CD lehetőség a [GitHubról,](#configure-deployment-source)és a következő lépéseket teszi a CI/CD beállításhoz:

- Egy GitHub Actions munkafolyamatfájlt a GitHub-adattárba, hogy kezelni tudja az összeállítási és üzembe helyezési App Service.
- Hozzáadja az alkalmazás közzétételi profilját GitHub-titokként. A munkafolyamat-fájl ezt a titkos adatokat használja a hitelesítéshez App Service.
- Rögzíti a munkafolyamat futtatási naplóiból  [származó](https://docs.github.com/actions/managing-workflow-runs/using-workflow-run-logs) adatokat, és megjeleníti azokat az alkalmazás Üzembe helyezési központjának **Naplók lapján.**

Az alkalmazás-GitHub Actions a következő módokon szabhatja testre:

- Testreszabhatja a munkafolyamat-fájlt, miután az már létre van hozva a GitHub-adattárban. További információ: [Munkafolyamat-szintaxis GitHub Actions.](https://docs.github.com/actions/reference/workflow-syntax-for-github-actions) Csak győződjön meg arról, hogy a munkafolyamat App Service [azure/webapps-deploy művelet](https://github.com/Azure/webapps-deploy) használatával.
- Ha a kiválasztott ág védett, akkor is megtekintheti a munkafolyamat-fájl előnézetét a konfiguráció mentése nélkül, majd manuálisan hozzáadhatja azt az adattárhoz. Ez a metódus nem adja meg a napló és a Azure Portal.
- Közzétételi profil helyett az üzembe helyezést [szolgáltatásnév](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) használatával kell Azure Active Directory.

#### <a name="authenticate-with-a-service-principal"></a>Hitelesítés szolgáltatásnévvel

Ez a választható konfiguráció lecseréli az alapértelmezett hitelesítést a létrehozott munkafolyamat-fájlban található közzétételi profilokra.

1. Hozzon létre egy egyszerű szolgáltatást [az az ad sp create-for-rbac paranccsal](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) az [Azure CLI-n.](/cli/azure/) A következő példában cserélje le a *\<subscription-id>* , *\<group-name>* és *\<app-name>* értékeket a saját értékeire:

    ```azurecli-interactive
    az ad sp create-for-rbac --name "myAppDeployAuth" --role contributor \
                                --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                                --sdk-auth
    ```
    
    > [!IMPORTANT]
    > A biztonság érdekében adja meg a minimálisan szükséges hozzáférést a szolgáltatásnévhez. Az előző példában a hatókör nem a teljes erőforráscsoportra, App Service alkalmazásra van korlátozva.
    
1. Mentse a teljes JSON-kimenetet a következő lépéshez, beleértve a legfelső szintű kimenetet `{}` is.

1. A [GitHubon](https://github.com/)keresse meg az adattárat, és válassza a Beállítások > **Titkos > Új titkos kód hozzáadása lehetőséget.**

1. Illessze be az Azure CLI-parancs teljes JSON-kimenetét a titkos gombra. Adjon a titkos nak egy nevet, `AZURE_CREDENTIALS` például: .

1. Az Üzembe helyezési központ által létrehozott munkafolyamat-fájlban módosítsa a lépést az alábbi példához hasonló kóddal (amelyet egy Node.js `azure/webapps-deploy` módosít):

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

Windows-alkalmazások esetében manuálisan konfigurálhatja a folyamatos üzembe helyezést egy felhőalapú Git- vagy Mercurial-adattárból, amely a portál által közvetlenül nem támogatott, például a [GitLab.](https://gitlab.com/) Ezt a Source (Forrás) legördülő menü External Git (Külső **Git) legördülő menüében érhetők** el. További információ: Folyamatos üzembe helyezés [beállítása manuális lépésekkel.](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps)

## <a name="more-resources"></a>További erőforrások

* [Üzembe helyezés az Azure Pipelinesból az Azure App Services](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps)
* [A folyamatos üzembe helyezés gyakori problémáinak kivizsgálása](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Azure PowerShell használatával](/powershell/azure/)
* [A Kudu projekt](https://github.com/projectkudu/kudu/wiki)

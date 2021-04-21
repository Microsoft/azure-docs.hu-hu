---
title: Üzembe helyezési hitelesítő adatok konfigurálása
description: Megtudhatja, hogy milyen típusú üzembe helyezési hitelesítő Azure App Service és hogyan konfigurálhatja és használhatja őket.
ms.topic: article
ms.date: 02/11/2021
ms.reviewer: byvinyal
ms.custom: seodec18, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 008bfa58c117fc1b43227ba73902d921cec25795
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830574"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Központi telepítési hitelesítő adatok konfigurálása Azure App Service
Az alkalmazások helyi számítógépről történő [](./overview.md) központi telepítésének biztonságossá Azure App Service a helyi [Git-telepítéshez](deploy-local-git.md) és az FTP/S telepítéshez kétféle hitelesítő [adattípust támogat.](deploy-ftp.md) Ezek a hitelesítő adatok nem azonosak az Azure-előfizetés hitelesítő adataival.

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

> [!NOTE]
> A **fejlesztői központ (klasszikus)** Azure Portal, amely a régi üzembe helyezési folyamat, 2021 márciusában elavult lesz. Ez a módosítás nem érinti az alkalmazás meglévő telepítési beállításait, és az alkalmazástelepítést a Központi telepítési központ **lapon folytathatja.**

## <a name="configure-user-scope-credentials"></a><a name="userscope"></a>Felhasználói hatókör hitelesítő adatainak konfigurálása

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Futtassa [az az webapp deployment user set](/cli/azure/webapp/deployment/user#az_webapp_deployment_user_set) parancsot. Cserélje le \<username> \<password> a és a helyére az üzembe helyezési felhasználó felhasználónevét és jelszavát. 

- A felhasználónévnek egyedinek kell lennie az Azure-ban, és a helyi Git-leküldések számára nem tartalmazhatja a "@" szimbólumot. 
- A jelszónak legalább nyolc karakter hosszúságúnak kell lennie, és a következő három elemből kettőnek kell lennie: betűk, számok és szimbólumok. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

A JSON-kimenetben a jelszó a `null` következő: .

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

A felhasználói hatókör hitelesítő adatai nem konfigurálhatóak Azure PowerShell. Használjon másik módszert, vagy fontolja meg [az alkalmazáshatókörű hitelesítő adatok használatát.](#appscope) 

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

A felhasználói hatókör hitelesítő adatait bármely alkalmazás erőforráslapján [konfigurálhatja.](../azure-resource-manager/management/manage-resources-portal.md#manage-resources) Függetlenül attól, hogy melyik alkalmazásban konfigurálja ezeket a hitelesítő adatokat, az Azure-fiókban az összes előfizetésre vonatkozik. 

A [Azure Portal](https://portal.azure.com)az üzembe helyezési hitelesítő adatok oldalának eléréséhez legalább egy alkalmazással kell rendelkeznie. A felhasználói hatókör hitelesítő adatainak konfigurálása:

1. Az alkalmazás bal oldali menüjében válassza az Üzembehely > FTPS hitelesítő adatok vagy a Helyi  >   **Git/FTPS hitelesítő adatok lehetőséget.**

    ![Bemutatja, hogyan választhatja ki az FTP-irányítópultot az Azure-beli üzembe helyezési App Services.](./media/app-service-deployment-credentials/access-no-git.png)

2. Görgessen le **a Felhasználói hatókör beállításhoz,** konfigurálja a **Felhasználónevet** és **a Jelszót,** majd válassza a **Mentés lehetőséget.**

Miután beállította az üzembe helyezési hitelesítő adatokat, a *Git* üzembe helyezési felhasználónevét az alkalmazás Áttekintés **oldalán találja.**

![Bemutatja, hogyan találhatja meg a Git üzemelő példány felhasználónevét az alkalmazás Áttekintés lapján.](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Ha a Git üzembe helyezése konfigurálva van, a lapon megjelenik egy **Git-/üzembe helyezési felhasználónév**; Ellenkező esetben egy **FTP-/üzembe helyezési felhasználónevet .**

> [!NOTE]
> Az Azure nem mutatja a felhasználói hatókör üzembe helyezési jelszavát. Ha elfelejtette a jelszót, az ebben a szakaszban található lépéseket követve alaphelyzetbe állíthatja a hitelesítő adatait.
>
> 

-----

## <a name="use-user-scope-credentials-with-ftpftps"></a>Felhasználói hatókör hitelesítő adatainak használata FTP-/FTPS-sel

Az FTP-/FTPS-végpontok felhasználói hatókörű hitelesítő adatokkal történő hitelesítéséhez a következő formátumú felhasználónevet kell megadni: `<app-name>\<user-name>`

Mivel a felhasználói hatókör hitelesítő adatai a felhasználóhoz vannak kapcsolva, és nem egy adott erőforráshoz, a felhasználónévnek ebben a formátumban kell lennie ahhoz, hogy a bejelentkezési műveletet a megfelelő alkalmazásvégpontra irányítsa.

## <a name="get-application-scope-credentials"></a><a name="appscope"></a>Alkalmazáshatókör hitelesítő adatainak lekérte

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Az az [webapp deployment list-publishing-profiles](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) paranccsal szerezze be az alkalmazáshatókörű hitelesítő adatokat. Például:

```azurecli-interactive
az webapp deployment list-publishing-profiles --resource-group <group-name> --name <app-name>
```

Helyi [Git üzemelő példány](deploy-local-git.md)esetén az az [webapp deployment list-publishing-credentials](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_credentials) paranccsal is lekérhetők az alkalmazás távoli Git URI-ját, amelybe már be vannak ágyazva az alkalmazáshatókörű hitelesítő adatok. Például:

```azurecli-interactive
az webapp deployment list-publishing-credentials --resource-group <group-name> --name <app-name> --query scmUri
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Szerezze be az alkalmazáshatókör hitelesítő adatait a [Get-AzWebAppPublishingProfile paranccsal.](/powershell/module/az.websites/get-azwebapppublishingprofile) Például:

```azurepowershell-interactive
Get-AzWebAppPublishingProfile -ResourceGroupName <group-name> -Name <app-name>
```

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. Az alkalmazás bal oldali menüjében válassza az Üzembe **helyezési** központ FTPS hitelesítő adatai vagy  >   a **Helyi Git/FTPS hitelesítő adatok lehetőséget.**

    ![Bemutatja, hogyan választhatja ki az FTP-irányítópultot az Azure-beli üzembe helyezési App Services.](./media/app-service-deployment-credentials/access-no-git.png)

2. Az Alkalmazás **hatóköre** szakaszban  válassza a Másolás hivatkozást a felhasználónév vagy jelszó másolásához.

-----

## <a name="reset-application-scope-credentials"></a>Alkalmazáshatókör hitelesítő adatainak alaphelyzetbe állítása

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Állítsa alaphelyzetbe az alkalmazáshatókör hitelesítő adatait az [az resource invoke-action paranccsal:](/cli/azure/resource#az_resource_invoke_action)

```azurecli-interactive
az resource invoke-action --action newpassword --resource-group <group-name> --name <app-name> --resource-type Microsoft.Web/sites
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Állítsa alaphelyzetbe az alkalmazáshatókör hitelesítő adatait az [Invoke-AzResourceAction paranccsal:](/powershell/module/az.resources/invoke-azresourceaction)

```azurepowershell-interactive
Invoke-AzResourceAction -ResourceGroupName <group-name> -ResourceType Microsoft.Web/sites -ResourceName <app-name> -Action newpassword
```

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. Az alkalmazás bal oldali menüjében válassza az **Üzembe** helyezési központ FTPS hitelesítő adatai vagy  >   a **Helyi Git/FTPS hitelesítő adatok lehetőséget.**

    ![Bemutatja, hogyan választhatja ki az FTP-irányítópultot az Azure-beli üzembe helyezési App Services.](./media/app-service-deployment-credentials/access-no-git.png)

2. Az Alkalmazás **hatóköre szakaszban** válassza a Alaphelyzetbe **állítás lehetőséget.**

-----

## <a name="disable-basic-authentication"></a>Alapszintű hitelesítés letiltása

Egyes szervezeteknek meg kell felelnie a biztonsági követelményeknek, és inkább letiltanák az FTP-n vagy a WebDeployn keresztüli hozzáférést. Így a szervezet tagjai csak olyan API-kon keresztül App Services férhetnek hozzá az Azure Active Directory (Azure AD) által vezérelt API-kon keresztül.

### <a name="ftp"></a>FTP

A hely FTP-hozzáférésének letiltásához futtassa a következő CLI-parancsot. Cserélje le a helyőrzőket az erőforráscsoportra és a hely nevére. 

```azurecli-interactive
az resource update --resource-group <resource-group> --name ftp --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

Annak megerősítéséhez, hogy az FTP-hozzáférés le van tiltva, megpróbálhat hitelesítést végezni egy FTP-ügyfél, például a FileZilla használatával. A közzétételi hitelesítő adatok lekéréséhez kattintson a webhely áttekintési paneljére, és kattintson a Közzétételi profil letöltése elemre. A hitelesítéshez használja a fájl FTP-gazdanevét, felhasználónevét és jelszavát, és 401-es hibaüzenetet kap, amely szerint Ön nem jogosult.

### <a name="webdeploy-and-scm"></a>WebDeploy és SCM

A WebDeploy-porthoz és az SCM-helyhez való alapszintű hitelesítési hozzáférés letiltásához futtassa a következő CLI-parancsot. Cserélje le a helyőrzőket az erőforráscsoportra és a hely nevére. 

```azurecli-interactive
az resource update --resource-group <resource-group> --name scm --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

Annak megerősítéséhez, hogy a közzétételi profil hitelesítő adatai le vannak tiltva a WebDeployban, próbáljon meg közzétenni egy [webalkalmazást a 2019-es Visual Studio használatával.](/visualstudio/deployment/quickstart-deploy-to-azure)

### <a name="disable-access-to-the-api"></a>Az API-hoz való hozzáférés letiltása

Az előző szakaszban található API az Azure szerepköralapú hozzáférés-vezérlését (Azure [](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role) RBAC) használja, ami azt jelenti, hogy létrehozhat egy egyéni szerepkört, és alacsonyabb jogosultságú felhasználókat rendelhet hozzá a szerepkörhöz, így azok nem tudják engedélyezni az alapszintű hitelesítést egyetlen helyen sem. Az egyéni szerepkör konfiguráláshoz kövesse [az alábbi utasításokat.](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#create-a-custom-rbac-role)

Használhatja a [Azure Monitor](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#audit-with-azure-monitor) is a sikeres hitelesítési kérések naplózására, és Azure Policy a konfiguráció kényszerítését az előfizetésben található összes webhelyre. [](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#enforce-compliance-with-azure-policy)

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan használhatja ezeket a hitelesítő adatokat az alkalmazás helyi [Gitből](deploy-local-git.md) történő üzembe helyezéséhez vagy [FTP/S használatával.](deploy-ftp.md)

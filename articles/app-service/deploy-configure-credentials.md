---
title: Üzembe helyezési hitelesítő adatok konfigurálása
description: Megtudhatja, milyen típusú központi telepítési hitelesítő adatok vannak Azure App Serviceban, és hogyan konfigurálhatja és használhatja azokat.
ms.topic: article
ms.date: 02/11/2021
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: 2a53ecb1b3411561da50f7dbf3be79f9d70b42bc
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100560430"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Központi telepítési hitelesítő adatok konfigurálása Azure App Servicehoz
Ha egy helyi számítógépről szeretné biztonságossá tenni az alkalmazások telepítését, [Azure app Service](./overview.md) támogatja a [helyi git-telepítés](deploy-local-git.md) és az [FTP/S üzembe helyezése](deploy-ftp.md)során a hitelesítő adatok két típusát. Ezek a hitelesítő adatok nem egyeznek meg az Azure-előfizetés hitelesítő adataival.

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

## <a name="configure-user-scope-credentials"></a><a name="userscope"></a>Felhasználói hatókörű hitelesítő adatok konfigurálása

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Futtassa az az [WebApp Deployment User set](/cli/azure/webapp/deployment/user#az-webapp-deployment-user-set) parancsot. Cserélje \<username> le \<password> a és a értékét egy üzembe helyezési felhasználói felhasználónévre és jelszóra. 

- A felhasználónévnek egyedinek kell lennie az Azure-ban, a helyi git-leküldések esetében pedig nem tartalmazhatja a "@" szimbólumot. 
- A jelszónak legalább nyolc karakterből kell állnia, és a következő három elem közül kettőnek kell lennie: betűk, számok és szimbólumok. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

A JSON-kimenet a jelszót jeleníti meg `null` .

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

A felhasználói hatókörhöz tartozó hitelesítő adatok nem konfigurálhatók Azure PowerShell. Használjon másik módszert, vagy vegye fontolóra az [alkalmazás-hatókörbeli hitelesítő adatok](#appscope)használatát. 

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

A felhasználói hatókörhöz tartozó hitelesítő adatokat bármely alkalmazás [erőforrás-oldalán](../azure-resource-manager/management/manage-resources-portal.md#manage-resources)konfigurálhatja. Függetlenül attól, hogy melyik alkalmazáshoz konfigurálja ezeket a hitelesítő adatokat, az Azure-fiók összes előfizetésének összes alkalmazására vonatkozik. 

A [Azure Portalben](https://portal.azure.com)legalább egy alkalmazásnak rendelkeznie kell a központi telepítési hitelesítő adatok lap eléréséhez. Felhasználói hatókörű hitelesítő adatok konfigurálása:

1. Az alkalmazás bal oldali menüjében válassza a > **Deployment Center**  >  **FTPS hitelesítő adatok** vagy a **helyi git/FTPS hitelesítő adatok** lehetőséget.

    ![Bemutatja, hogyan választhatja ki az FTP-irányítópultot az Azure App Services központi telepítési központból.](./media/app-service-deployment-credentials/access-no-git.png)

2. Görgessen le a **felhasználói hatókörre**, konfigurálja a **felhasználónevet** és a **jelszót**, majd kattintson a **Mentés** gombra.

Miután beállította az üzembe helyezési hitelesítő adatokat, megkeresheti a *git* üzembe helyezési felhasználónevet az alkalmazás **Áttekintés** lapján.

![Bemutatja, hogyan keresheti meg a git üzembe helyezési felhasználónevet az alkalmazás Áttekintés oldalán.](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Ha a git üzembe helyezése be van állítva, akkor a lap a **git/Deployment felhasználónevet** jeleníti meg; egyéb esetben egy **FTP-/telepítési Felhasználónév**.

> [!NOTE]
> Az Azure nem jeleníti meg a felhasználói hatókör telepítési jelszavát. Ha elfelejti a jelszót, az ebben a szakaszban ismertetett lépéseket követve alaphelyzetbe állíthatja a hitelesítő adatait.
>
> 

-----

## <a name="use-user-scope-credentials-with-ftpftps"></a>Felhasználói hatókörű hitelesítő adatok használata FTP/FTPS

A felhasználói hatókörű hitelesítő adatokkal rendelkező FTP-/FTPS-végpontra történő hitelesítéshez a felhasználónévnek a következő formátumúnak kell lennie: `<app-name>\<user-name>`

Mivel a felhasználói hatókörhöz tartozó hitelesítő adatok a felhasználóhoz vannak társítva, nem egy adott erőforráshoz, a felhasználónévnek ebben a formátumban kell lennie ahhoz, hogy a bejelentkezési műveletet a megfelelő alkalmazás-végpontra irányítsa.

## <a name="get-application-scope-credentials"></a><a name="appscope"></a>Alkalmazás-hatókörű hitelesítő adatok beolvasása

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Az alkalmazás-hatókörű hitelesítő adatok beszerzése az az [WebApp Deployment List-Publishing-Profiles](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) paranccsal. Például:

```azurecli-interactive
az webapp deployment list-publishing-profiles --resource-group <group-name> --name <app-name>
```

A [helyi git üzembe helyezéséhez](deploy-local-git.md)használhatja az az [WebApp Deployment List-Publishing-hitelesítőadats](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_credentials) parancsot az alkalmazáshoz tartozó git távoli URI beszerzéséhez, az alkalmazás-hatókör hitelesítő adataival már beágyazva. Például:

```azurecli-interactive
az webapp deployment list-publishing-credentials --resource-group <group-name> --name <app-name> --query scmUri
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Kérje le az alkalmazás-hatókörű hitelesítő adatokat a [Get-AzWebAppPublishingProfile](/powershell/module/az.websites/get-azwebapppublishingprofile) parancs használatával. Például:

```azurepowershell-interactive
Get-AzWebAppPublishingProfile -ResourceGroupName <group-name> -Name <app-name>
```

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. Az alkalmazás bal oldali menüjében válassza a **Deployment Center**  >  **FTPS hitelesítő adatok** vagy a **helyi git/FTPS hitelesítő adatok** lehetőséget.

    ![Bemutatja, hogyan választhatja ki az FTP-irányítópultot az Azure App Services központi telepítési központból.](./media/app-service-deployment-credentials/access-no-git.png)

2. Az **alkalmazás hatóköre** szakaszban válassza a **Másolás** hivatkozást a Felhasználónév vagy a jelszó másolásához.

-----

## <a name="reset-application-scope-credentials"></a>Alkalmazás-hatókörű hitelesítő adatok alaphelyzetbe állítása

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Az alkalmazás-hatókörű hitelesítő adatok alaphelyzetbe állítása az az [Resource meghívása-Action](/cli/azure/resource#az_resource_invoke_action) parancs használatával:

```azurecli-interactive
az resource invoke-action --action newpassword --resource-group <group-name> --name <app-name> --resource-type Microsoft.Web/sites
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Az alkalmazás-hatókörbeli hitelesítő adatok alaphelyzetbe állítása a [Meghívási-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) parancs használatával:

```azurepowershell-interactive
Invoke-AzResourceAction -ResourceGroupName <group-name> -ResourceType Microsoft.Web/sites -ResourceName <app-name> -Action newpassword
```

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. Az alkalmazás bal oldali menüjében válassza a **Deployment Center**  >  **FTPS hitelesítő adatok** vagy a **helyi git/FTPS hitelesítő adatok** lehetőséget.

    ![Bemutatja, hogyan választhatja ki az FTP-irányítópultot az Azure App Services központi telepítési központból.](./media/app-service-deployment-credentials/access-no-git.png)

2. Az **alkalmazás hatóköre** szakaszban válassza az **Alaphelyzetbe állítás** lehetőséget.

-----

## <a name="disable-basic-authentication"></a>Alapszintű hitelesítés letiltása

Néhány szervezetnek meg kell felelnie a biztonsági követelményeknek, és inkább le kell tiltania az FTP-n vagy a webtelepítésen keresztüli hozzáférést Így a szervezet tagjai csak az Azure Active Directory (Azure AD) által vezérelt API-kkal érhetik el a App Services.

### <a name="ftp"></a>FTP

Ha le szeretné tiltani az FTP-hozzáférést a helyhez, futtassa az alábbi CLI-parancsot. Cserélje le a helyőrzőket az erőforráscsoport és a webhely nevére. 

```azurecli-interactive
az resource update --resource-group <resource-group> --name ftp --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

Annak ellenőrzéséhez, hogy az FTP-hozzáférés le van-e tiltva, megpróbálhat hitelesítést végezni egy FTP-ügyfél, például a FileZilla használatával. A közzétételi hitelesítő adatok lekéréséhez lépjen a webhely áttekintés paneljére, és kattintson a közzétételi profil letöltése elemre. A hitelesítéshez használja a fájl FTP-állomásnevét, felhasználónevét és jelszavát, és egy 401-es hibaüzenetet kap, amely azt jelzi, hogy Ön nem jogosult.

### <a name="webdeploy-and-scm"></a>Webdeploy és SCM

Ha le szeretné tiltani az alapszintű hitelesítési hozzáférést a webtelepítési porthoz és az SCM-helyhez, futtassa az alábbi CLI-parancsot. Cserélje le a helyőrzőket az erőforráscsoport és a webhely nevére. 

```azurecli-interactive
az resource update --resource-group <resource-group> --name scm --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

Annak ellenőrzéséhez, hogy a közzétételi profil hitelesítő adatai le vannak-e tiltva a webtelepítésben, próbáljon meg [közzétenni egy webalkalmazást a Visual Studio 2019 használatával](/visualstudio/deployment/quickstart-deploy-to-azure).

### <a name="disable-access-to-the-api"></a>Az API elérésének letiltása

Az előző szakaszban található API egy Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC), ami azt jelenti, hogy [létrehozhat egy egyéni szerepkört](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role) , és alacsonyabb priveldged felhasználókat rendelhet hozzá a szerepkörhöz, így az alapszintű hitelesítés nem engedélyezhető egyetlen helyen sem. Az egyéni szerepkör konfigurálásához [kövesse az alábbi utasításokat](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#create-a-custom-rbac-role).

A [Azure monitor](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#audit-with-azure-monitor) használatával is naplózhatja a sikeres hitelesítési kérelmeket, és a [Azure Policy](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#enforce-compliance-with-azure-policy) használatával kényszerítheti ki ezt a konfigurációt az előfizetésben található összes webhelyre.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan használhatja ezeket a hitelesítő adatokat az alkalmazás [helyi git](deploy-local-git.md) -ből való üzembe helyezéséhez, vagy [FTP/S](deploy-ftp.md)használatával.

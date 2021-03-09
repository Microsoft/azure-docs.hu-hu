---
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 05/11/2020
ms.openlocfilehash: 2d186463f340be14113228baa583fdcf6ff55401
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102511072"
---
## <a name="authenticate-with-azure-active-directory"></a>Hitelesítés az Azure Active Directoryval

> [!IMPORTANT]
> 1. Jelenleg **csak** a Computer Vision API, a Face API, a Text Analytics API, a teljes olvasó, az űrlap-felismerő, a anomália-detektor, a QnA Maker és az összes Bing-szolgáltatás, kivéve Bing Custom Search az Azure Active Directory (HRE) használatával történő hitelesítést támogatja.
> 2. Az HRE-hitelesítést mindig az Azure-erőforrás egyéni altartománynevével együtt kell használni. A [regionális végpontok](../articles/cognitive-services/cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) nem TÁMOGATJÁK a HRE-hitelesítést.

Az előző szakaszokban bemutatjuk, hogyan végezheti el a hitelesítést az Azure Cognitive Services az egy vagy több szolgáltatást használó előfizetési kulccsal. Habár ezek a kulcsok gyors és egyszerű elérési utat biztosítanak a fejlesztés megkezdéséhez, olyan összetettebb forgatókönyvekhez tartoznak, amelyek Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) igényelnek. Nézzük meg, mi szükséges a hitelesítéshez Azure Active Directory (HRE) használatával.

A következő fejezetekben a Azure Cloud Shell vagy az Azure CLI használatával hozhat létre altartományokat, szerepköröket rendelhet hozzá, és beszerezhet egy tulajdonosi jogkivonatot az Azure-Cognitive Services meghívásához. Ha elakad, a rendszer a Azure Cloud Shell/Azure CLI-ben minden parancshoz elérhető összes lehetőséget tartalmazó hivatkozásokat tartalmaz.

### <a name="create-a-resource-with-a-custom-subdomain"></a>Erőforrás létrehozása egyéni altartománnyal

Első lépésként hozzon létre egy egyéni altartományt. Ha olyan meglévő Cognitive Services-erőforrást szeretne használni, amely nem rendelkezik egyéni altartomány nevével, kövesse a [Cognitive Services egyéni altartományok](../articles/cognitive-services/cognitive-services-custom-subdomains.md#how-does-this-impact-existing-resources) című témakör utasításait az erőforráshoz tartozó egyéni altartományok engedélyezéséhez.

1. Először nyissa meg a Azure Cloud Shell. Ezután [válasszon ki egy előfizetést](/powershell/module/az.accounts/set-azcontext):

   ```powershell-interactive
   Set-AzContext -SubscriptionName <SubscriptionName>
   ```

2. Ezután [hozzon létre egy Cognitive Services erőforrást](/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount) egy egyéni altartománnyal. Az altartomány nevének globálisan egyedinek kell lennie, és nem tartalmazhat speciális karaktereket (például: ".", "!", ",").

   ```powershell-interactive
   $account = New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <ACCOUNT_NAME> -Type <ACCOUNT_TYPE> -SkuName <SUBSCRIPTION_TYPE> -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>
   ```

3. Ha a művelet sikeres, a **végpontnak** az erőforrás egyedi altartománynevét kell megjelenítenie.


### <a name="assign-a-role-to-a-service-principal"></a>Szerepkör kiosztása egy egyszerű szolgáltatáshoz

Most, hogy rendelkezik az erőforráshoz tartozó egyéni altartománnyal, hozzá kell rendelnie egy szerepkört egy egyszerű szolgáltatáshoz.

> [!NOTE]
> Ne feledje, hogy az Azure-szerepkör-hozzárendelések akár öt percet is igénybe vehetnek.

1. Először regisztráljon egy [HRE alkalmazást](/powershell/module/Az.Resources/New-AzADApplication).

   ```powershell-interactive
   $SecureStringPassword = ConvertTo-SecureString -String <YOUR_PASSWORD> -AsPlainText -Force

   $app = New-AzADApplication -DisplayName <APP_DISPLAY_NAME> -IdentifierUris <APP_URIS> -Password $SecureStringPassword
   ```

   A következő lépésben szüksége lesz a **ApplicationId** .

2. Ezután [létre kell hoznia egy szolgáltatásnevet](/powershell/module/az.resources/new-azadserviceprincipal) a HRE alkalmazáshoz.

   ```powershell-interactive
   New-AzADServicePrincipal -ApplicationId <APPLICATION_ID>
   ```

   >[!NOTE]
   > Ha a Azure Portalban regisztrál egy alkalmazást, ez a lépés az Ön számára lesz elvégezve.

3. Az utolsó lépés a ["Cognitive Services user" szerepkör társítása](/powershell/module/az.Resources/New-azRoleAssignment) az egyszerű szolgáltatáshoz (hatóköre az erőforrás). Szerepkör hozzárendelésével a szolgáltatás egyszerű hozzáférést biztosít ehhez az erőforráshoz. Az előfizetés több erőforrásához is biztosíthatja ugyanazt a szolgáltatást.
   >[!NOTE]
   > Az egyszerű szolgáltatásnév ObjectId van használatban, nem az alkalmazás ObjectId.
   > A ACCOUNT_ID a létrehozott Cognitive Services fiók Azure-erőforrás-azonosítója lesz. Az Azure Resource id-t a Azure Portal erőforrás "tulajdonságok" területén találja.

   ```azurecli-interactive
   New-AzRoleAssignment -ObjectId <SERVICE_PRINCIPAL_OBJECTID> -Scope <ACCOUNT_ID> -RoleDefinitionName "Cognitive Services User"
   ```

### <a name="sample-request"></a>Példa a kérelemre

Ebben a példában egy jelszót használunk az egyszerű szolgáltatás hitelesítéséhez. A rendszer a megadott jogkivonatot használja a Computer Vision API meghívásához.

1. A **TenantId** beszerzése:
   ```powershell-interactive
   $context=Get-AzContext
   $context.Tenant.Id
   ```

2. Token beszerzése:
   > [!NOTE]
   > Ha Azure Cloud Shell használ, az `SecureClientSecret` osztály nem érhető el. 

   #### <a name="powershell"></a>[PowerShell](#tab/powershell)
   ```powershell-interactive
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList "https://login.windows.net/<TENANT_ID>"
   $secureSecretObject = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.SecureClientSecret" -ArgumentList $SecureStringPassword   
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $app.ApplicationId, $secureSecretObject
   $token=$authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", $clientCredential).Result
   $token
   ```
   
   #### <a name="azure-cloud-shell"></a>[Azure Cloud Shell](#tab/azure-cloud-shell)
   ```Azure Cloud Shell
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList "https://login.windows.net/<TENANT_ID>"
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $app.ApplicationId, <YOUR_PASSWORD>
   $token=$authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", $clientCredential).Result
   $token
   ``` 

   ---

3. Hívja meg a Computer Vision API:
   ```powershell-interactive
   $url = $account.Endpoint+"vision/v1.0/models"
   $result = Invoke-RestMethod -Uri $url  -Method Get -Headers @{"Authorization"=$token.CreateAuthorizationHeader()} -Verbose
   $result | ConvertTo-Json
   ```

Másik lehetőségként az egyszerű szolgáltatás hitelesítése tanúsítvánnyal végezhető el. Az egyszerű szolgáltatásnév mellett a felhasználói rendszerbiztonsági tag is támogatott azáltal, hogy egy másik HRE-alkalmazásban delegált engedélyekkel rendelkezik. Ebben az esetben a jelszavak vagy tanúsítványok helyett a felhasználókat a rendszer a token beszerzése során a kétfaktoros hitelesítésre kéri.

## <a name="authorize-access-to-managed-identities"></a>Hozzáférés engedélyezése a felügyelt identitásokhoz
 
Cognitive Services támogatja Azure Active Directory (Azure AD) hitelesítését az [Azure-erőforrások felügyelt identitásával](../articles/active-directory/managed-identities-azure-resources/overview.md). Az Azure-erőforrások felügyelt identitásai engedélyezhetik Cognitive Services erőforrásokhoz való hozzáférést az Azure-beli virtuális gépeken (VM), a Function apps-ben, a virtuálisgép-méretezési csoportokban és más szolgáltatásokban futó alkalmazások Azure AD-beli hitelesítő adataival. Ha felügyelt identitásokat használ az Azure-erőforrásokhoz az Azure AD-hitelesítéssel együtt, elkerülheti a hitelesítő adatok tárolását a felhőben futó alkalmazásaival.  

### <a name="enable-managed-identities-on-a-vm"></a>Felügyelt identitások engedélyezése egy virtuális gépen

Mielőtt felügyelt identitásokat használ az Azure-erőforrásokhoz a virtuális gépről Cognitive Services erőforrásokhoz való hozzáférés engedélyezéséhez, engedélyeznie kell az Azure-erőforrások felügyelt identitásait a virtuális gépen. Az Azure-erőforrások felügyelt identitásának engedélyezéséről a következő témakörben tájékozódhat:

- [Azure Portalra](../articles/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../articles/active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../articles/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sablon](../articles/active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager ügyféloldali kódtárak](../articles/active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

A felügyelt identitásokkal kapcsolatos további információkért lásd: [felügyelt identitások az Azure-erőforrásokhoz](../articles/active-directory/managed-identities-azure-resources/overview.md).
---
title: Felügyelt identitások
description: Megtudhatja, hogyan működnek a felügyelt identitások Azure App Service és Azure Functions, hogyan konfigurálhat egy felügyelt identitást, és hogyan hozhat létre jogkivonatot a háttérerőforráshoz.
author: mattchenderson
ms.topic: article
ms.date: 05/27/2020
ms.author: mahender
ms.reviewer: yevbronsh
ms.custom: devx-track-csharp, devx-track-python, devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: badc6b6f1b45938e950ffadeefe30d81ed383440
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762442"
---
# <a name="how-to-use-managed-identities-for-app-service-and-azure-functions"></a>Felügyelt identitások használata App Service és Azure Functions

Ez a témakör bemutatja, hogyan hozhat létre felügyelt identitást App Service és Azure Functions alkalmazásokhoz, és hogyan használhatja más erőforrások eléréséhez. 

> [!Important] 
> A felügyelt identitások App Service és Azure Functions nem a várt módon viselkednek, ha az alkalmazást előfizetések/bérlők között migrálják. Az alkalmazásnak be kell szereznie egy új identitást, amely a funkció letiltásával és újra engedélyezésével történik. Lásd az [identitás eltávolítását alább.](#remove) Az lefelé irányuló erőforrásoknak az új identitás használatára vonatkozó hozzáférési szabályzatokat is frissíteniük kell.

[!INCLUDE [app-service-managed-identities](../../includes/app-service-managed-identities.md)]

## <a name="add-a-system-assigned-identity"></a>Rendszer által hozzárendelt identitás hozzáadása

A rendszer által hozzárendelt identitással való alkalmazáslétrehozáshoz további tulajdonságot kell beállítani az alkalmazáson.

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

A felügyelt identitás portálon való beállításához először a szokásos módon kell létrehoznia egy alkalmazást, majd engedélyeznie kell a funkciót.

1. Hozzon létre egy alkalmazást a portálon a szokásos módon. Keresse meg a portálon.

2. Ha függvényalkalmazást használ, lépjen a **Platformfunkciók lapra.** Más alkalmazástípusok esetén görgessen le a **Beállítások csoporthoz** a bal oldali navigációs sávon.

3. Válassza az **Identitás lehetőséget.**

4. A Rendszer által **hozzárendelt lapon** kapcsolja be az **Állapot** **kapcsolót.** Kattintson a **Mentés** gombra.

    ![Képernyőkép az állapot bekapcsolás helyének beállításáról, majd a Mentés gombra való váltásról.](media/app-service-managed-service-identity/system-assigned-managed-identity-in-azure-portal.png)


> [!NOTE] 
> A webalkalmazás vagy a pontalkalmazás felügyelt identitásának megkereséhez a Azure Portal a Vállalati alkalmazások területen keresse meg a **Felhasználói beállítások** szakaszt. A tárolóhely neve általában a következőre `<app name>/slots/<slot name>` hasonlít: .


### <a name="using-the-azure-cli"></a>Az Azure CLI-vel

Ha felügyelt identitást az Azure CLI használatával kell beállítania, az parancsot kell használnia `az webapp identity assign` egy meglévő alkalmazáson. Az ebben a szakaszban található példák futtatására három lehetőség közül választhat:

- Használja [Azure Cloud Shell](../cloud-shell/overview.md) a Azure Portal.
- A beágyazott Azure Cloud Shell a lenti kódblokkok jobb felső sarkában található "Próbálja ki" gombbal.
- [Ha helyi CLI-konzolt](/cli/azure/install-azure-cli) szeretne használni, telepítse az Azure CLI legújabb verzióját (2.0.31-es vagy újabb verzió). 

A következő lépések végigkik egy webalkalmazás létrehozásának és identitásnak a CLI használatával való hozzárendelésének folyamatán:

1. Ha az Azure CLI-t helyi konzolban használja, akkor először az [az login](/cli/azure/reference-index#az_login) paranccsal jelentkezzen be az Azure-ba. Olyan fiókot használjon, amely ahhoz az Azure-előfizetéshez van társítva, amelyben üzembe szeretné helyezni az alkalmazást:

    ```azurecli-interactive
    az login
    ```

2. Webalkalmazás létrehozása a CLI használatával. További példák a CLI és a App Service használatára: App Service [CLI-minták:](../app-service/samples-cli.md)

    ```azurecli-interactive
    az group create --name myResourceGroup --location westus
    az appservice plan create --name myPlan --resource-group myResourceGroup --sku S1
    az webapp create --name myApp --resource-group myResourceGroup --plan myPlan
    ```

3. Futtassa `identity assign` az parancsot az alkalmazás identitásának létrehozásához:

    ```azurecli-interactive
    az webapp identity assign --name myApp --resource-group myResourceGroup
    ```

### <a name="using-azure-powershell"></a>Az Azure PowerShell használata

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A következő lépések végigkik egy alkalmazás létrehozásának és identitás hozzárendelésének lépéseit a Azure PowerShell. A webalkalmazások és függvényalkalmazások létrehozására vonatkozó utasítások eltérnek.

#### <a name="using-azure-powershell-for-a-web-app"></a>A Azure PowerShell használata webalkalmazáshoz

1. Ha szükséges, telepítse a Azure PowerShell az útmutatóban található [Azure PowerShell,](/powershell/azure/)majd futtassa a következőt az Azure-ral való `Login-AzAccount` kapcsolat létrehozásához: .

2. Webalkalmazás létrehozása a Azure PowerShell. További példák a Azure PowerShell és App Service: App Service [PowerShell-minták:](../app-service/samples-powershell.md)

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an App Service plan in Free tier.
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName $resourceGroupName -Tier Free

    # Create a web app.
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName $resourceGroupName
    ```

3. Futtassa `Set-AzWebApp -AssignIdentity` az parancsot az alkalmazás identitásának létrehozásához:

    ```azurepowershell-interactive
    Set-AzWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName $resourceGroupName 
    ```

#### <a name="using-azure-powershell-for-a-function-app"></a>A Azure PowerShell használata függvényalkalmazáshoz

1. Ha szükséges, telepítse a Azure PowerShell az útmutatóban található [Azure PowerShell,](/powershell/azure/)majd futtassa a következőt az Azure-ral való `Login-AzAccount` kapcsolat létrehozásához: .

2. Függvényalkalmazás létrehozása a Azure PowerShell. További példákat az Azure PowerShell és Azure Functions [az Az.Functions-referenciában talál:](/powershell/module/az.functions/#functions)

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account.
    New-AzStorageAccount -Name $storageAccountName -ResourceGroupName $resourceGroupName -SkuName $sku

    # Create a function app with a system-assigned identity.
    New-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -Location $location -StorageAccountName $storageAccountName -Runtime $runtime -IdentityType SystemAssigned
    ```

Ehelyett frissíthet egy meglévő függvényalkalmazást is a `Update-AzFunctionApp` használatával.

### <a name="using-an-azure-resource-manager-template"></a>Sablon Azure Resource Manager használata

Egy Azure Resource Manager sablonnal automatizálhatja az Azure-erőforrások üzembe helyezését. További információ a App Service és a Functionsben való üzembe helyezésről: [Automating resource deployment in App Service](../app-service/deploy-complex-application-predictably.md) and [Automating resource deployment](../azure-functions/functions-infrastructure-as-code.md)in Azure Functions .

Bármilyen típusú erőforrást létre lehet hozva egy identitással, ha az alábbi tulajdonságot adja meg az `Microsoft.Web/sites` erőforrás-definícióban:

```json
"identity": {
    "type": "SystemAssigned"
}
```

> [!NOTE]
> Egy alkalmazás egyszerre lehet rendszer- és felhasználó által hozzárendelt identitással is. Ebben az esetben a `type` tulajdonság a következő lenne: `SystemAssigned,UserAssigned`

A rendszer által hozzárendelt típus hozzáadása arra utasítja az Azure-t, hogy hozza létre és kezelje az alkalmazás identitását.

Egy webalkalmazás például a következőre hasonlíthat:

```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
    ]
}
```

A hely létrehozása után a következő további tulajdonságokkal rendelkezik:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

A tenantId tulajdonság azonosítja azt az Azure AD-bérlőt, amelyhez az identitás tartozik. A principalId az alkalmazás új identitásának egyedi azonosítója. Az Azure AD-ban a szolgáltatásnév ugyanaz a név, mint amit a App Service vagy Azure Functions adott.

Ha ezekre a tulajdonságokra a sablon egy későbbi szakaszában hivatkozni [ `reference()` ](../azure-resource-manager/templates/template-functions-resource.md#reference) kell, ezt a sablon függvényen keresztül, a jelzővel is megadhatja, ahogyan az `'Full'` alábbi példában is:

```json
{
    "tenantId": "[reference(resourceId('Microsoft.Web/sites', variables('appName')), '2018-02-01', 'Full').identity.tenantId]",
    "objectId": "[reference(resourceId('Microsoft.Web/sites', variables('appName')), '2018-02-01', 'Full').identity.principalId]",
}
```

## <a name="add-a-user-assigned-identity"></a>Felhasználó által hozzárendelt identitás hozzáadása

Ha felhasználó által hozzárendelt identitással hoz létre egy alkalmazást, létre kell hoznia az identitást, majd hozzá kell adni az erőforrás-azonosítóját az alkalmazás konfigurációhoz.

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

Először létre kell hoznia egy felhasználó által hozzárendelt identitás-erőforrást.

1. Hozzon létre egy felhasználó által hozzárendelt felügyelt identitás erőforrást a [következő utasítások szerint:](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

2. Hozzon létre egy alkalmazást a portálon a szokásos módon. Keresse meg a portálon.

3. Ha függvényalkalmazást használ, lépjen a **Platformfunkciók lapra.** Más alkalmazástípusok esetén görgessen le a **Beállítások csoporthoz** a bal oldali navigációs sávon.

4. Válassza az **Identitás lehetőséget.**

5. A **Felhasználóhoz rendelt lapon** kattintson a Hozzáadás **elemre.**

6. Keresse meg és válassza ki a korábban létrehozott identitást. Kattintson a **Hozzáadás** parancsra.

    ![Felügyelt identitás a App Service](media/app-service-managed-service-identity/user-assigned-managed-identity-in-azure-portal.png)

### <a name="using-azure-powershell"></a>Az Azure PowerShell használata

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A következő lépések végigveszik az alkalmazások létrehozásának és identitások hozzárendelésének folyamatán a Azure PowerShell.

> [!NOTE]
> Az alkalmazáshoz Azure PowerShell parancsmagok aktuális Azure App Service nem támogatják a felhasználó által hozzárendelt identitásokat. Az alábbi utasítások a következőkre Azure Functions.

1. Szükség esetén telepítse a Azure PowerShell az útmutatóban [](/powershell/azure/)található Azure PowerShell, majd futtassa a következőt az Azure-ral `Login-AzAccount` való kapcsolat létrehozásához: .

2. Függvényalkalmazás létrehozása a Azure PowerShell. További példákat a függvényekkel való Azure PowerShell az Azure Functions [Az.Functions-referenciában talál.](/powershell/module/az.functions/#functions) Az alábbi szkript azt is használja, amelyet külön kell telepíteni a Felhasználó által hozzárendelt felügyelt identitás létrehozása, listának vagy törlése a `New-AzUserAssignedIdentity` Azure PowerShell. [](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account.
    New-AzStorageAccount -Name $storageAccountName -ResourceGroupName $resourceGroupName -SkuName $sku

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Create a function app with a user-assigned identity.
    New-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -Location $location -StorageAccountName $storageAccountName -Runtime $runtime -IdentityType UserAssigned -IdentityId $userAssignedIdentity.Id
    ```

Meglévő függvényalkalmazást a használatával is `Update-AzFunctionApp` frissíthet.

### <a name="using-an-azure-resource-manager-template"></a>Sablon Azure Resource Manager használata

Egy Azure Resource Manager sablonnal automatizálhatja az Azure-erőforrások üzembe helyezését. További információ az üzembe helyezésről a App Service és a Functionsben: [Automating resource deployment in App Service](../app-service/deploy-complex-application-predictably.md) and [Automating resource deployment](../azure-functions/functions-infrastructure-as-code.md)in Azure Functions .

Bármilyen típusú erőforrást létre lehet hozva egy identitással úgy, hogy a következő blokkot beleveszi az erőforrás-definícióba, a helyett pedig a kívánt `Microsoft.Web/sites` `<RESOURCEID>` identitás erőforrás-azonosítóját adja meg:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

> [!NOTE]
> Az alkalmazások egyszerre kaphatnak rendszer- és felhasználó által hozzárendelt identitásokat is. Ebben az esetben a `type` tulajdonság a következő lenne: `SystemAssigned,UserAssigned`

A felhasználó által hozzárendelt típus hozzáadása arra utasítja az Azure-t, hogy az alkalmazáshoz megadott, felhasználó által hozzárendelt identitást használja.

Egy webalkalmazás például a következőre hasonlíthat:

```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
        }
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
    ]
}
```

A hely létrehozása után a következő további tulajdonságokkal rendelkezik:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

A principalId az Azure AD-felügyelethez használt identitás egyedi azonosítója. A clientId az alkalmazás új identitásának egyedi azonosítója, amely a futásidejű hívások során használni kívánt identitás megadására használatos.

## <a name="obtain-tokens-for-azure-resources"></a>Azure-erőforrások jogkivonatának beszerzése

Az alkalmazások a felügyelt identitásuk használatával jogkivonatokat kaphatnak az Azure AD által védett egyéb erőforrásokhoz való hozzáféréshez, például Azure Key Vault. Ezek a jogkivonatok az erőforráshoz hozzáférő alkalmazást képviselik, nem az alkalmazás egyetlen konkrét felhasználóját. 

Előfordulhat, hogy konfigurálnia kell a célerőforrást, hogy engedélyezze a hozzáférést az alkalmazásból. Ha például jogkivonatot kér a Key Vault eléréséhez, meg kell győződni arról, hogy hozzáadott egy hozzáférési szabályzatot, amely tartalmazza az alkalmazás identitását. Ellenkező esetben a Key Vault hívásait a rendszer elutasítja, még akkor is, ha tartalmazzák a jogkivonatot. Ha többet szeretne megtudni arról, hogy mely erőforrások támogatják a Azure Active Directory a jogkivonatokat, tekintse meg az [Azure AD-hitelesítést támogató Azure-szolgáltatásokat.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

> [!IMPORTANT]
> A felügyelt identitások háttérszolgáltatásai erőforrás-URI-nként körülbelül 24 óráig fenntartják a gyorsítótárat. Ha frissíti egy adott célerőforrás hozzáférési szabályzatát, és azonnal lekér egy jogkivonatot az adott erőforráshoz, akkor továbbra is lekérhet egy elavult engedélyekkel rendelkező gyorsítótárazott jogkivonatot, amíg a jogkivonat le nem jár. Jogkivonat frissítését jelenleg nem lehet kényszeríteni.

A jogkivonatok beszerzéséhez egy egyszerű REST-protokoll áll App Service és Azure Functions. Ez minden alkalmazáshoz és nyelvhez használható. A .NET és a Java esetében az Azure SDK absztrakciót biztosít a protokoll felett, és helyi fejlesztési élményt biztosít.

### <a name="using-the-rest-protocol"></a>A REST protokoll használata

> [!NOTE]
> A protokoll egy régebbi verziója, amely a "2017-09-01" API-verziót használja, a fejlécet használta a helyett, és csak a felhasználó által hozzárendelt tulajdonságot fogadta `secret` `X-IDENTITY-HEADER` `clientid` el. Emellett időbélyeg `expires_on` formátumban visszaadta a et. MSI_ENDPOINT használható a IDENTITY_ENDPOINT aliasaként, MSI_SECRET a IDENTITY_HEADER. A protokollnak erre a verziójára jelenleg szükség van a Linux-használat üzemeltetési csomagokhoz.

A felügyelt identitással rendelkezik egy alkalmazáshoz két környezeti változó van definiálva:

- IDENTITY_ENDPOINT – a helyi jogkivonat-szolgáltatás URL-címe.
- IDENTITY_HEADER – a kiszolgálóoldali kérelmek hamisítási (SSRF) támadásának mérséklése érdekében használt fejléc. Az értéket a platform forgatja.

A **IDENTITY_ENDPOINT** egy helyi URL-cím, amelyről az alkalmazás jogkivonatokat kérhet. Egy erőforrás jogkivonatának lekérése érdekében egy HTTP GET kérést kell kérnie erre a végpontra, beleértve a következő paramétereket:

> | Paraméter neve    | In     | Description                                                                                                                                                                                                                                                                                                                                |
> |-------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | Erőforrás          | Lekérdezés  | Annak az erőforrásnak az Azure AD-erőforrás URI-ját, amelyhez jogkivonatot kell beszerezni. Ez lehet az [Azure AD-hitelesítést](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) támogató Azure-szolgáltatások egyike vagy bármely más erőforrás-URI.    |
> | api-verzió       | Lekérdezés  | A használni használt token API verziója. Használja a "2019-08-01" vagy újabb lehetőséget (kivéve, ha a Linux-használat-t használja, amely jelenleg csak a "2017-09-01" lehetőséget kínálja – lásd a fenti megjegyzést).                                                                                                                                                                                                                                                                 |
> | X-IDENTITY-HEADER | Fejléc | A IDENTITY_HEADER környezeti változó értéke. Ez a fejléc segít mérsékelni a kiszolgálóoldali hamisítási (SSRF) támadásokat.                                                                                                                                                                                                    |
> | client_id         | Lekérdezés  | (Nem kötelező) A felhasználó által hozzárendelt identitás ügyfél-azonosítója. Nem használható olyan kérések esetén, amelyek , `principal_id` `mi_res_id` vagy et `object_id` tartalmaznak. Ha az összes azonosítóparaméter ( , , és ) nincs megadva, a rendszer `client_id` `principal_id` a rendszer által `object_id` `mi_res_id` hozzárendelt identitást használja.                                             |
> | principal_id      | Lekérdezés  | (Nem kötelező) A felhasználó által hozzárendelt identitás egyszerű azonosítója. `object_id` A egy alias, amely ehelyett használható. Nem használható olyan kérések esetén, amelyek client_id, mi_res_id vagy object_id. Ha az összes azonosítóparaméter ( , , és ) nincs megadva, a rendszer `client_id` `principal_id` a rendszer által `object_id` `mi_res_id` hozzárendelt identitást használja. |
> | mi_res_id         | Lekérdezés  | (Nem kötelező) A használni szükséges felhasználó által hozzárendelt identitás Azure-erőforrás-azonosítója. Nem használható olyan kérések esetén, amelyek , `principal_id` `client_id` vagy et `object_id` tartalmaznak. Ha az összes azonosítóparaméter ( , , és ) nincs megadva, a rendszer `client_id` `principal_id` a rendszer által `object_id` `mi_res_id` hozzárendelt identitást használja.                                      |

> [!IMPORTANT]
> Ha felhasználó által hozzárendelt identitásokhoz próbál jogkivonatokat beszerezni, meg kell tartalmaznia az egyik választható tulajdonságot. Ellenkező esetben a jogkivonat-szolgáltatás megpróbál jogkivonatot beszerezni egy rendszer által hozzárendelt identitáshoz, amely lehet, hogy létezik vagy nem létezik.

A 200 OK sikeres válasz egy JSON-törzset tartalmaz, amely a következő tulajdonságokkal rendelkezik:

> | Tulajdonság neve | Description                                                                                                                                                                                                                                        |
> |---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | access_token  | A kért hozzáférési jogkivonat. A hívó webszolgáltatás ezzel a jogkivonattal hitelesítheti magát a fogadó webszolgáltatásban.                                                                                                                               |
> | client_id     | A használt identitás ügyfél-azonosítója.                                                                                                                                                                                                       |
> | expires_on    | A hozzáférési jogkivonat lejárati ideje. A dátumot az "1970-01-01T0:0:0Z UTC" időponttól (UTC) származó másodpercek száma jelöli (a jogkivonat jogcímének `exp` felel meg).                                                                                |
> | not_before    | A hozzáférési jogkivonat effektusának időkorrekta, amely elfogadható. A dátumot az "1970-01-01T0:0:0Z UTC" időponttól (a jogkivonat jogcímének megfelelő) másodpercek száma `nbf` jelöli.                                                      |
> | Erőforrás      | Az az erőforrás, amelyre a hozzáférési jogkivonatot lekérte, és amely megegyezik a kérés lekérdezési `resource` sztring paraméterének értékére.                                                                                                                               |
> | token_type    | A jogkivonat típusának értékét jelzi. Az Azure AD csak a Bearer típust támogatja. A bearer tokenekkel kapcsolatos további információkért lásd: [The OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750) ( Az OAuth 2.0 engedélyezési keretrendszere: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt)). |

Ez a válasz ugyanaz, mint az Azure AD szolgáltatásokhoz való hozzáférési [jogkivonat-kérésének válasza.](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#service-to-service-access-token-response)

### <a name="rest-protocol-examples"></a>REST-protokoll – példák

Egy példa kérés a következőre hasonlíthat:

```http
GET /MSI/token?resource=https://vault.azure.net&api-version=2019-08-01 HTTP/1.1
Host: localhost:4141
X-IDENTITY-HEADER: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```

A mintaválasz az alábbihoz hasonló lehet:

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
    "access_token": "eyJ0eXAi…",
    "expires_on": "1586984735",
    "resource": "https://vault.azure.net",
    "token_type": "Bearer",
    "client_id": "5E29463D-71DA-4FE0-8E69-999B57DB23B0"
}
```

### <a name="code-examples"></a>Kódpéldák

# <a name="net"></a>[.NET](#tab/dotnet)

> [!TIP]
> A .NET-nyelvek esetében a [Microsoft.Azure.Services.AppAuthentication](#asal) szolgáltatást is használhatja ahelyett, hogy saját maga hozta volna azt elő.

```csharp
private readonly HttpClient _client;
// ...
public async Task<HttpResponseMessage> GetToken(string resource)  {
    var request = new HttpRequestMessage(HttpMethod.Get, 
        String.Format("{0}/?resource={1}&api-version=2019-08-01", Environment.GetEnvironmentVariable("IDENTITY_ENDPOINT"), resource));
    request.Headers.Add("X-IDENTITY-HEADER", Environment.GetEnvironmentVariable("IDENTITY_HEADER"));
    return await _client.SendAsync(request);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const rp = require('request-promise');
const getToken = function(resource, cb) {
    let options = {
        uri: `${process.env["IDENTITY_ENDPOINT"]}/?resource=${resource}&api-version=2019-08-01`,
        headers: {
            'X-IDENTITY-HEADER': process.env["IDENTITY_HEADER"]
        }
    };
    rp(options)
        .then(cb);
}
```

# <a name="python"></a>[Python](#tab/python)

```python
import os
import requests

identity_endpoint = os.environ["IDENTITY_ENDPOINT"]
identity_header = os.environ["IDENTITY_HEADER"]

def get_bearer_token(resource_uri):
    token_auth_uri = f"{identity_endpoint}?resource={resource_uri}&api-version=2019-08-01"
    head_msi = {'X-IDENTITY-HEADER':identity_header}

    resp = requests.get(token_auth_uri, headers=head_msi)
    access_token = resp.json()['access_token']

    return access_token
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:IDENTITY_ENDPOINT + "?resource=$resourceURI&api-version=2019-08-01"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"X-IDENTITY-HEADER"="$env:IDENTITY_HEADER"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```

---

### <a name="using-the-microsoftazureservicesappauthentication-library-for-net"></a><a name="asal"></a>A Microsoft.Azure.Services.AppAuthentication kódtár használata a .NET-hez

A .NET-alkalmazások és -függvények esetében a felügyelt identitások használatának legegyszerűbb módja a Microsoft.Azure.Services.AppAuthentication csomag használata. Ez a kódtár azt is lehetővé teszi, hogy helyileg tesztelje a kódot a fejlesztői gépen az Visual Studio-ból, az [Azure CLI-ről](/cli/azure)vagy az integrált Active Directory használatával. A felhőben üzemeltetett szolgáltatás alapértelmezés szerint rendszer által hozzárendelt identitást használ, de ezt a viselkedést testre szabhatja egy kapcsolati sztring környezeti változóval, amely a felhasználó által hozzárendelt identitás ügyfél-azonosítójára hivatkozik. További információ a kódtár fejlesztési lehetőségeiről: [Microsoft.Azure.Services.AppAuthentication reference (Microsoft.Azure.Services.AppAuthentication referencia).] Ez a szakasz bemutatja, hogyan ássa el a kódtárat a kódban.

1. Adjon hozzá hivatkozásokat a [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) és bármely más szükséges NuGet-csomaghoz az alkalmazáshoz. Az alábbi példa a [Microsoft.Azure.KeyVault szolgáltatásokat is használja.](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)

2. Adja hozzá a következő kódot az alkalmazáshoz úgy, hogy a megfelelő erőforrást célozza meg. Ez a példa két módszert mutat be a Azure Key Vault:

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```

Ha felhasználó által hozzárendelt felügyelt identitást szeretne használni, beállíthatja az `AzureServicesAuthConnectionString` alkalmazásbeállítást a következőre: `RunAs=App;AppId=<clientId-guid>` . Cserélje le a helyére a használni `<clientId-guid>` kívánt identitás ügyfél-azonosítóját. Több ilyen kapcsolati sztringet is meghatározhat egyéni alkalmazásbeállítások használatával, és az értékeiket átadhatja az AzureServiceTokenProvider konstruktornak.

```csharp
    var identityConnectionString1 = Environment.GetEnvironmentVariable("UA1_ConnectionString");
    var azureServiceTokenProvider1 = new AzureServiceTokenProvider(identityConnectionString1);
    
    var identityConnectionString2 = Environment.GetEnvironmentVariable("UA2_ConnectionString");
    var azureServiceTokenProvider2 = new AzureServiceTokenProvider(identityConnectionString2);
```

Az AzureServiceTokenProvider konfigurálásával és az elérhetővé tévő műveletekkel kapcsolatos további információkért tekintse meg a [Microsoft.Azure.Services.AppAuthentication] referenciát, valamint a [App Service és a KeyVault MSI .NET-minta](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)használatával való használatát.

### <a name="using-the-azure-sdk-for-java"></a>A Javához készült Azure SDK használata

Java-alkalmazások és -függvények esetén a felügyelt identitások használatának legegyszerűbb módja a Javához készült [Azure SDK.](https://github.com/Azure/azure-sdk-for-java) Ez a szakasz bemutatja, hogyan ássa el a kódtárat a kódban.

1. Adjon hozzá egy, az [Azure SDK-kódtárra vonatkozó hivatkozást.](https://mvnrepository.com/artifact/com.microsoft.azure/azure) Maven-projektek esetén hozzáadhatja ezt a kódrészletet a projekt `dependencies` POM-fájlja szakaszhoz:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure</artifactId>
        <version>1.23.0</version>
    </dependency>
    ```

2. Használja a `AppServiceMSICredentials` objektumot a hitelesítéshez. Ez a példa bemutatja, hogyan használható ez a mechanizmus a Azure Key Vault:

    ```java
    import com.microsoft.azure.AzureEnvironment;
    import com.microsoft.azure.management.Azure;
    import com.microsoft.azure.management.keyvault.Vault
    //...
    Azure azure = Azure.authenticate(new AppServiceMSICredentials(AzureEnvironment.AZURE))
            .withSubscription(subscriptionId);
    Vault myKeyVault = azure.vaults().getByResourceGroup(resourceGroup, keyvaultName);

    ```


## <a name="remove-an-identity"></a><a name="remove"></a>Identitás eltávolítása

A rendszer által hozzárendelt identitás úgy távolítható el, ha a portál, a PowerShell vagy a parancssori felület használatával letiltja a funkciót, ugyanúgy, ahogyan azt létrehozták. A felhasználó által hozzárendelt identitások egyenként távolíthatók el. Az összes identitás eltávolításához állítsa az identitás típusát "Nincs" típusra.

A rendszer által hozzárendelt identitás ily módon való eltávolítása az Azure AD-ból is törli azt. Az alkalmazás-erőforrás törlésekor a rendszer automatikusan eltávolítja a rendszer által hozzárendelt identitásokat az Azure AD-ból.

Egy ARM-sablon összes [identitásának eltávolítása:](#using-an-azure-resource-manager-template)

```json
"identity": {
    "type": "None"
}
```

Az összes identitás eltávolítása a Azure PowerShell (csak Azure Functions esetén):

```azurepowershell-interactive
# Update an existing function app to have IdentityType "None".
Update-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -IdentityType None
```

> [!NOTE]
> Emellett egy alkalmazásbeállítás is beállítható, amely WEBSITE_DISABLE_MSI, ami csak letiltja a helyi jogkivonat-szolgáltatást. Az identitást azonban a helyén hagyja, és az eszközök továbbra is bekapcsoltként vagy engedélyezettként fogják mutatni a felügyelt identitást. Ennek eredményeképpen ez a beállítás nem ajánlott.

## <a name="next-steps"></a>Következő lépések

- [Hozzáférés SQL Database felügyelt identitással](app-service-web-tutorial-connect-msi.md)
- [Az Azure Storage biztonságos elérése felügyelt identitás használatával](scenario-secure-app-access-storage.md)
- [A Microsoft Graph biztonságos hívása felügyelt identitás használatával](scenario-secure-app-access-microsoft-graph-as-app.md)

[Microsoft.Azure.Services.AppAuthentication referencia]: /dotnet/api/overview/azure/service-to-service-authentication
---
title: Felügyelt identitások
description: Ismerje meg, hogyan működnek a felügyelt identitások Azure App Service és Azure Functions, hogyan konfigurálhatja a felügyelt identitásokat, és hogyan hozhatja ki a jogkivonatot a háttérbeli erőforrásokhoz.
author: mattchenderson
ms.topic: article
ms.date: 05/27/2020
ms.author: mahender
ms.reviewer: yevbronsh
ms.custom: devx-track-csharp, devx-track-python, devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 16cd4685f513eb628372802cc158195b81bce72a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98736171"
---
# <a name="how-to-use-managed-identities-for-app-service-and-azure-functions"></a>Felügyelt identitások használata App Service és Azure Functions

Ebből a témakörből megtudhatja, hogyan hozhat létre felügyelt identitást App Service és Azure Functions alkalmazásokhoz, és hogyan használhatja azt más erőforrásokhoz való hozzáféréshez. 

> [!Important] 
> A App Service és Azure Functions felügyelt identitásai nem a várt módon fognak működni, ha az alkalmazást áttelepítik az előfizetések/bérlők között. Az alkalmazásnak új identitást kell beszereznie, amely a funkció letiltásával és újbóli engedélyezésével végezhető el. Lásd [az alábbi identitások eltávolítását](#remove) ismertető szakaszt. Az alárendelt erőforrásoknak is szükségük van a hozzáférési szabályzatok frissítésére az új identitás használatához.

[!INCLUDE [app-service-managed-identities](../../includes/app-service-managed-identities.md)]

## <a name="add-a-system-assigned-identity"></a>Rendszerhez rendelt identitás hozzáadása

A rendszer által hozzárendelt identitással rendelkező alkalmazások létrehozásához további tulajdonságot kell beállítani az alkalmazásban.

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

Ha felügyelt identitást szeretne beállítani a portálon, először hozzon létre egy alkalmazást a megszokott módon, majd engedélyezze a szolgáltatást.

1. A szokásos módon hozzon létre egy alkalmazást a portálon. Navigáljon a portálon.

2. Ha Function alkalmazást használ, navigáljon a **platform szolgáltatásaihoz**. Más típusú alkalmazások esetén görgessen le a **Beállítások** csoportba a bal oldali navigációs sávon.

3. Válassza az **identitás** lehetőséget.

4. A **rendszerhez rendelt** lapon váltson az **állapot** bekapcsolva **értékre**. Kattintson a **Mentés** gombra.

    ![Képernyőfelvétel: az állapot bekapcsolásának helye, majd válassza a mentés lehetőséget.](media/app-service-managed-service-identity/system-assigned-managed-identity-in-azure-portal.png)


> [!NOTE] 
> Ha szeretné megkeresni a Web App vagy a slot alkalmazás felügyelt identitását a Azure Portalban, a **vállalati alkalmazások** területen tekintse meg a **felhasználói beállítások** szakaszt. A tárolóhely neve általában a következőhöz hasonló: `<app name>/slots/<slot name>` .


### <a name="using-the-azure-cli"></a>Az Azure CLI-vel

Felügyelt identitás Azure CLI használatával történő beállításához a `az webapp identity assign` parancsot egy meglévő alkalmazáshoz kell használnia. Ebben a szakaszban három lehetőség van a példák futtatására:

- Használja [Azure Cloud Shell](../cloud-shell/overview.md) a Azure Portal.
- A beágyazott Azure Cloud Shell az alábbi, az egyes kódrészletek jobb felső sarkában található "kipróbálás" gomb segítségével érheti el.
- [Telepítse az Azure CLI legújabb verzióját](/cli/azure/install-azure-cli) (2.0.31 vagy újabb), ha helyi CLI-konzolt szeretne használni. 

A következő lépések végigvezetik a webalkalmazások létrehozásán és az identitás a CLI használatával történő hozzárendelésén.

1. Ha az Azure CLI-t helyi konzolban használja, akkor először az [az login](/cli/azure/reference-index#az-login) paranccsal jelentkezzen be az Azure-ba. Olyan fiókot használjon, amely ahhoz az Azure-előfizetéshez van társítva, amelyben az alkalmazást üzembe szeretné helyezni:

    ```azurecli-interactive
    az login
    ```

2. Hozzon létre egy webalkalmazást a parancssori felület használatával. A CLI és a App Service használatával kapcsolatos további Példákért lásd: [app Service CLI-minták](../app-service/samples-cli.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location westus
    az appservice plan create --name myPlan --resource-group myResourceGroup --sku S1
    az webapp create --name myApp --resource-group myResourceGroup --plan myPlan
    ```

3. Futtassa a `identity assign` parancsot az alkalmazás identitásának létrehozásához:

    ```azurecli-interactive
    az webapp identity assign --name myApp --resource-group myResourceGroup
    ```

### <a name="using-azure-powershell"></a>Az Azure PowerShell használata

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az alábbi lépések végigvezetik az alkalmazás létrehozásán és identitásának Azure PowerShell használatával történő hozzárendelésén. A webalkalmazások és a functions-alkalmazások létrehozásának utasításai eltérőek.

#### <a name="using-azure-powershell-for-a-web-app"></a>Azure PowerShell használata webalkalmazásokhoz

1. Szükség esetén telepítse a Azure PowerShell a [Azure PowerShell útmutatóban](/powershell/azure/)található utasításokkal, majd futtassa a parancsot az `Login-AzAccount` Azure-hoz való kapcsolódáshoz.

2. Webalkalmazás létrehozása Azure PowerShell használatával. A Azure PowerShell és a App Service használatával kapcsolatos további példákért tekintse meg az [app Service PowerShell-mintákat](../app-service/samples-powershell.md):

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an App Service plan in Free tier.
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName $resourceGroupName -Tier Free

    # Create a web app.
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName $resourceGroupName
    ```

3. Futtassa a `Set-AzWebApp -AssignIdentity` parancsot az alkalmazás identitásának létrehozásához:

    ```azurepowershell-interactive
    Set-AzWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName $resourceGroupName 
    ```

#### <a name="using-azure-powershell-for-a-function-app"></a>Azure PowerShell használata egy Function-alkalmazáshoz

1. Szükség esetén telepítse a Azure PowerShell a [Azure PowerShell útmutatóban](/powershell/azure/)található utasításokkal, majd futtassa a parancsot az `Login-AzAccount` Azure-hoz való kapcsolódáshoz.

2. Function-alkalmazás létrehozása Azure PowerShell használatával. A Azure PowerShell és a Azure Functions használatával kapcsolatos további példákért tekintse meg az az [. functions hivatkozást](/powershell/module/az.functions/#functions):

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account.
    New-AzStorageAccount -Name $storageAccountName -ResourceGroupName $resourceGroupName -SkuName $sku

    # Create a function app with a system-assigned identity.
    New-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -Location $location -StorageAccountName $storageAccountName -Runtime $runtime -IdentityType SystemAssigned
    ```

Egy meglévő Function alkalmazást is frissíthet a `Update-AzFunctionApp` helyett.

### <a name="using-an-azure-resource-manager-template"></a>Azure Resource Manager sablon használata

Az Azure-erőforrások üzembe helyezésének automatizálásához Azure Resource Manager sablon használható. Ha többet szeretne megtudni a App Service és a függvények üzembe helyezéséről, olvassa el az [erőforrás-telepítés automatizálása app Service](../app-service/deploy-complex-application-predictably.md) és az [erőforrás-telepítés automatizálása a Azure functions-ben](../azure-functions/functions-infrastructure-as-code.md)című témakört.

Bármely típusú erőforrás `Microsoft.Web/sites` létrehozható identitással, az erőforrás-definícióban a következő tulajdonsággal együtt:

```json
"identity": {
    "type": "SystemAssigned"
}
```

> [!NOTE]
> Egy alkalmazáshoz a rendszerhez hozzárendelt és felhasználó által hozzárendelt identitások is tartozhatnak egyszerre. Ebben az esetben a tulajdonság a következő: `type``SystemAssigned,UserAssigned`

A rendszer által hozzárendelt típus hozzáadásával az Azure létrehozza és kezeli az alkalmazás identitását.

Egy webalkalmazás például a következőhöz hasonló lehet:

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

A hely létrehozásakor a következő tulajdonságokkal rendelkezik:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

A tenantId tulajdonság azt határozza meg, hogy az identitás melyik Azure AD-bérlőhöz tartozik. A principalId az alkalmazás új identitásának egyedi azonosítója. Az Azure AD-n belül az egyszerű szolgáltatásnév neve megegyezik a App Service vagy Azure Functions példányával.

Ha ezeket a tulajdonságokat a sablon egy későbbi szakaszában kell megadnia, ezt megteheti a [ `reference()` sablon függvény](../azure-resource-manager/templates/template-functions-resource.md#reference) használatával a `'Full'` jelölővel, ahogy az alábbi példában látható:

```json
{
    "tenantId": "[reference(resourceId('Microsoft.Web/sites', variables('appName')), '2018-02-01', 'Full').identity.tenantId]",
    "objectId": "[reference(resourceId('Microsoft.Web/sites', variables('appName')), '2018-02-01', 'Full').identity.principalId]",
}
```

## <a name="add-a-user-assigned-identity"></a>Felhasználó által hozzárendelt identitás hozzáadása

A felhasználó által hozzárendelt identitással rendelkező alkalmazások létrehozásához létre kell hoznia az identitást, majd hozzá kell adnia annak erőforrás-azonosítóját az alkalmazás-konfigurációhoz.

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

Először létre kell hoznia egy felhasználó által hozzárendelt identitás-erőforrást.

1. Hozzon létre egy felhasználó által hozzárendelt felügyelt identitási erőforrást az [utasításoknak](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)megfelelően.

2. A szokásos módon hozzon létre egy alkalmazást a portálon. Navigáljon a portálon.

3. Ha Function alkalmazást használ, navigáljon a **platform szolgáltatásaihoz**. Más típusú alkalmazások esetén görgessen le a **Beállítások** csoportba a bal oldali navigációs sávon.

4. Válassza az **identitás** lehetőséget.

5. A **felhasználó által hozzárendelt** lapon kattintson a **Hozzáadás** gombra.

6. Keresse meg a korábban létrehozott identitást, és válassza ki. Kattintson a **Hozzáadás** parancsra.

    ![Felügyelt identitás a App Serviceban](media/app-service-managed-service-identity/user-assigned-managed-identity-in-azure-portal.png)

### <a name="using-azure-powershell"></a>Az Azure PowerShell használata

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az alábbi lépések végigvezetik az alkalmazás létrehozásán és identitásának Azure PowerShell használatával történő hozzárendelésén.

> [!NOTE]
> A Azure App Service Azure PowerShell parancsmagok jelenlegi verziója nem támogatja a felhasználó által hozzárendelt identitásokat. Az alábbi utasítások a Azure Functions.

1. Szükség esetén telepítse a Azure PowerShell a [Azure PowerShell útmutatóban](/powershell/azure/)található utasításokkal, majd futtassa a parancsot az `Login-AzAccount` Azure-hoz való kapcsolódáshoz.

2. Function-alkalmazás létrehozása Azure PowerShell használatával. A Azure PowerShell és a Azure Functions használatával kapcsolatos további példákért tekintse meg az az [. functions referenciát](/powershell/module/az.functions/#functions). Az alábbi szkript azt is lehetővé teszi, hogy `New-AzUserAssignedIdentity` külön kell telepíteni, mint a [Létrehozás, a felhasználó által hozzárendelt felügyelt identitások listázása vagy törlése Azure PowerShell használatával](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md).

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

Egy meglévő Function alkalmazást is frissíthet a `Update-AzFunctionApp` helyett.

### <a name="using-an-azure-resource-manager-template"></a>Azure Resource Manager sablon használata

Az Azure-erőforrások üzembe helyezésének automatizálásához Azure Resource Manager sablon használható. Ha többet szeretne megtudni a App Service és a függvények üzembe helyezéséről, olvassa el az [erőforrás-telepítés automatizálása app Service](../app-service/deploy-complex-application-predictably.md) és az [erőforrás-telepítés automatizálása a Azure functions-ben](../azure-functions/functions-infrastructure-as-code.md)című témakört.

Bármely típusú erőforrás `Microsoft.Web/sites` létrehozható identitással, az erőforrás-definíció következő blokkjának beírásával, a `<RESOURCEID>` kívánt identitás erőforrás-azonosítójával lecserélve:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

> [!NOTE]
> Egy alkalmazáshoz a rendszerhez hozzárendelt és felhasználó által hozzárendelt identitások is tartozhatnak egyszerre. Ebben az esetben a tulajdonság a következő: `type``SystemAssigned,UserAssigned`

A felhasználó által hozzárendelt típus hozzáadásával az Azure az alkalmazáshoz megadott felhasználó által hozzárendelt identitás használatára utasítja.

Egy webalkalmazás például a következőhöz hasonló lehet:

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

A hely létrehozásakor a következő tulajdonságokkal rendelkezik:

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

A principalId az Azure AD-felügyelethez használt identitás egyedi azonosítója. A clientId az alkalmazás új identitásának egyedi azonosítója, amely a futásidejű hívásokban használandó identitás megadására szolgál.

## <a name="obtain-tokens-for-azure-resources"></a>Jogkivonatok beszerzése az Azure-erőforrásokhoz

Egy alkalmazás a felügyelt identitásával lekérheti a jogkivonatokat az Azure AD által védett egyéb erőforrásokhoz, például a Azure Key Vaulthoz való hozzáféréshez. Ezek a tokenek az erőforráshoz hozzáférő alkalmazást, és nem az alkalmazás adott felhasználóját jelölik. 

Előfordulhat, hogy a cél erőforrást úgy kell konfigurálnia, hogy engedélyezze az alkalmazáshoz való hozzáférést. Ha például jogkivonatot kér Key Vaulthoz való hozzáféréshez, meg kell győződnie arról, hogy az alkalmazás identitását tartalmazó hozzáférési szabályzatot adott hozzá. Ellenkező esetben a rendszer elutasítja a Key Vault meghívásait, még akkor is, ha azok tartalmazzák a jogkivonatot. Ha többet szeretne megtudni arról, hogy mely erőforrások támogatják Azure Active Directory jogkivonatokat, tekintse meg az Azure [ad-hitelesítést támogató Azure-szolgáltatásokat](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

> [!IMPORTANT]
> A felügyelt identitások háttér-szolgáltatásai az erőforrás-URI-n keresztüli gyorsítótárat tartanak fenn körülbelül 24 óra alatt. Ha frissíti egy adott cél erőforrás hozzáférési házirendjét, és azonnal lekéri az adott erőforráshoz tartozó jogkivonatot, a jogkivonat lejárta után továbbra is elavult engedélyekkel rendelkező gyorsítótárazott tokent kaphat. Jelenleg nincs mód kényszeríteni a tokenek frissítését.

Létezik egy egyszerű REST-protokoll a jogkivonat beszerzéséhez App Service és Azure Functions. Ezt minden alkalmazáshoz és nyelvhez használhatja. A .NET és a Java esetében az Azure SDK absztrakciót biztosít a protokollon keresztül, és megkönnyíti a helyi fejlesztési élményt.

### <a name="using-the-rest-protocol"></a>A REST protokoll használata

> [!NOTE]
> A protokoll egy régebbi verziója, amely a "2017-09-01" API-verziót használja, a `secret` fejléc helyett a `X-IDENTITY-HEADER` (z), és csak a `clientid` felhasználó által hozzárendelt tulajdonságot fogadta el. A művelet időbélyeg formátumban is visszaadott `expires_on` . A MSI_ENDPOINT a IDENTITY_ENDPOINT aliasként használható, és a MSI_SECRET a IDENTITY_HEADER aliasként is használhatók. A protokoll ezen verzióját jelenleg a Linux-használat üzemeltetési csomagjaihoz kell megadni.

A felügyelt identitású alkalmazások esetében két környezeti változó van definiálva:

- IDENTITY_ENDPOINT – a helyi jogkivonat-szolgáltatás URL-címe.
- IDENTITY_HEADER – a kiszolgálóoldali kérelmek hamisításának (SSRF) elleni támadásának enyhítésére szolgáló fejléc. Az értéket a platform forgatja el.

A **IDENTITY_ENDPOINT** egy helyi URL-cím, amelyből az alkalmazás jogkivonatokat igényelhet. Egy erőforráshoz tartozó jogkivonat lekéréséhez hajtson végre egy HTTP GET kérelmet erre a végpontra, beleértve a következő paramétereket:

> | Paraméter neve    | In     | Leírás                                                                                                                                                                                                                                                                                                                                |
> |-------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | erőforrás          | Lekérdezés  | Annak az erőforrásnak az Azure AD erőforrás-URI azonosítója, amelynek a jogkivonatát meg kell szerezni. Ez lehet az egyik olyan [Azure-szolgáltatás, amely támogatja az Azure ad-hitelesítést](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) vagy bármilyen más erőforrás-URI-t.    |
> | api-verzió       | Lekérdezés  | A használni kívánt jogkivonat-API verziója. Használja a "2019-08-01" vagy az újabb verziót (kivéve, ha Linux-használatot használ, amely jelenleg csak a "2017-09-01"-ot kínálja) – lásd a fenti megjegyzést.                                                                                                                                                                                                                                                                 |
> | X-IDENTITY-HEADER | Fejléc | A IDENTITY_HEADER környezeti változó értéke. Ez a fejléc a kiszolgálóoldali kérelmek hamisításának (SSRF) elleni támadásának enyhítésére szolgál.                                                                                                                                                                                                    |
> | client_id         | Lekérdezés  | Választható A használni kívánt felhasználó által hozzárendelt identitás ügyfél-azonosítója. Nem használható olyan kérelemben, amely a következőt tartalmazza:, `principal_id` `mi_res_id` vagy `object_id` . Ha az összes azonosító paraméter ( `client_id` , `principal_id` ,, `object_id` és) ki `mi_res_id` van hagyva, a rendszer hozzárendelt identitást használja.                                             |
> | principal_id      | Lekérdezés  | Választható A használni kívánt felhasználó által hozzárendelt identitás résztvevő-azonosítója. `object_id` egy olyan alias, amely felhasználható helyette. Nem használható olyan kérelemben, amely client_id, mi_res_id vagy object_idt tartalmaz. Ha az összes azonosító paraméter ( `client_id` , `principal_id` ,, `object_id` és) ki `mi_res_id` van hagyva, a rendszer hozzárendelt identitást használja. |
> | mi_res_id         | Lekérdezés  | Választható A használni kívánt felhasználó által hozzárendelt identitás Azure-erőforrás-azonosítója. Nem használható olyan kérelemben, amely a következőt tartalmazza:, `principal_id` `client_id` vagy `object_id` . Ha az összes azonosító paraméter ( `client_id` , `principal_id` ,, `object_id` és) ki `mi_res_id` van hagyva, a rendszer hozzárendelt identitást használja.                                      |

> [!IMPORTANT]
> Ha a felhasználó által hozzárendelt identitásokhoz próbál jogkivonatokat beszerezni, a választható tulajdonságok egyikét kell tartalmaznia. Ellenkező esetben a jogkivonat-szolgáltatás megkísérli beolvasni a rendszerhez rendelt identitás tokenjét, amely esetleg nem létezik.

A sikeres 200 OK válasz egy JSON-törzset tartalmaz, amely a következő tulajdonságokkal rendelkezik:

> | Tulajdonság neve | Leírás                                                                                                                                                                                                                                        |
> |---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | access_token  | A kért hozzáférési jogkivonat. A hívó webszolgáltatás ezt a tokent használhatja a fogadó webszolgáltatáshoz való hitelesítéshez.                                                                                                                               |
> | client_id     | A használt identitás ügyfél-azonosítója.                                                                                                                                                                                                       |
> | expires_on    | A TimeSpan, amikor lejár a hozzáférési jogkivonat. A dátum az "1970-01-01T0:0: 0Z UTC" (a jogkivonat jogcímenek felel meg) másodpercben megadott számú másodperc `exp` .                                                                                |
> | not_before    | A TimeSpan, ha a hozzáférési jogkivonat érvénybe lép, és el lehet fogadni. A dátum az "1970-01-01T0:0: 0Z UTC" (a jogkivonat jogcímenek felel meg) másodpercben megadott számú másodperc `nbf` .                                                      |
> | erőforrás      | Az erőforráshoz a hozzáférési tokent kérték, amely megfelel a `resource` kérelem lekérdezési karakterlánc paraméterének.                                                                                                                               |
> | token_type    | Megadja a jogkivonat típusának értékét. Az Azure AD által támogatott egyetlen típus a tulajdonos. A tulajdonosi jogkivonatokkal kapcsolatos további információkért tekintse meg [a OAuth 2,0 engedélyezési keretrendszert: tulajdonosi jogkivonat használata (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |

Ez a válasz ugyanaz, mint az [Azure ad szolgáltatás – szolgáltatás hozzáférési jogkivonat-kérelemre adott válasz](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#service-to-service-access-token-response).

### <a name="rest-protocol-examples"></a>REST protokoll – példák

A kérelem példája a következőhöz hasonló lehet:

```http
GET /MSI/token?resource=https://vault.azure.net&api-version=2019-08-01 HTTP/1.1
Host: localhost:4141
X-IDENTITY-HEADER: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```

A minta válasz a következőhöz hasonlóan néz ki:

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
> A .NET nyelveken a [Microsoft. Azure. Services. AppAuthentication](#asal) is használhatja a kérelem elvégzése helyett.

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

### <a name="using-the-microsoftazureservicesappauthentication-library-for-net"></a><a name="asal"></a>A Microsoft. Azure. Services. AppAuthentication kódtár használata a .NET-hez

A .NET-alkalmazások és-függvények esetében a felügyelt identitással való munka legegyszerűbb módja a Microsoft. Azure. Services. AppAuthentication csomag. Ez a kódtár lehetővé teszi a kód helyi tesztelését a fejlesztői gépen, a Visual studióból, az [Azure CLI](/cli/azure)-ből vagy Active Directory integrált hitelesítésből származó felhasználói fiók használatával. Ha a felhőben üzemel, alapértelmezés szerint a rendszer által hozzárendelt identitást fogja használni, de ezt a viselkedést egy, a felhasználó által hozzárendelt identitás ügyfél-AZONOSÍTÓját használó kapcsolódási karakterlánc környezeti változóval is testreszabhatja. A könyvtárral kapcsolatos fejlesztési lehetőségekről további információt a [Microsoft. Azure. Services. AppAuthentication dokumentációjában]talál. Ez a szakasz bemutatja, hogyan kezdheti meg a kódtárat a kódban.

1. Adjon hozzá hivatkozásokat a [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) és minden más szükséges NuGet-csomaghoz az alkalmazásához. Az alábbi példa a [Microsoft. Azure.](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)kulcstartót is használja.

2. Adja hozzá a következő kódot az alkalmazáshoz, amely úgy van módosítva, hogy a megfelelő erőforrást célozza meg. Ez a példa két módszert mutat be a Azure Key Vaultsal való együttműködésre:

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```

Ha felhasználó által hozzárendelt felügyelt identitást szeretne használni, beállíthatja az alkalmazás beállítását a következőre: `AzureServicesAuthConnectionString` `RunAs=App;AppId=<clientId-guid>` . Cserélje le a helyére a `<clientId-guid>` használni kívánt identitás ügyfél-azonosítóját. Több ilyen összekapcsolási karakterláncot is meghatározhat egyéni Alkalmazásbeállítások használatával, és átadhatja az értékeket a AzureServiceTokenProvider konstruktorba.

```csharp
    var identityConnectionString1 = Environment.GetEnvironmentVariable("UA1_ConnectionString");
    var azureServiceTokenProvider1 = new AzureServiceTokenProvider(identityConnectionString1);
    
    var identityConnectionString2 = Environment.GetEnvironmentVariable("UA2_ConnectionString");
    var azureServiceTokenProvider2 = new AzureServiceTokenProvider(identityConnectionString2);
```

Ha többet szeretne megtudni a AzureServiceTokenProvider konfigurálásáról és az általa közzétett műveletekről, tekintse meg a [Microsoft. Azure. Services. AppAuthentication referenciáját] , valamint a app Service és a KULCSTARTÓT [MSI .net-minta](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)használatával.

### <a name="using-the-azure-sdk-for-java"></a>A Javához készült Azure SDK használata

A Java-alkalmazások és-függvények esetében a felügyelt identitással való munka legegyszerűbb módja a [Javához készült Azure SDK](https://github.com/Azure/azure-sdk-for-java). Ez a szakasz bemutatja, hogyan kezdheti meg a kódtárat a kódban.

1. Adjon hozzá egy hivatkozást az [Azure SDK-könyvtárhoz](https://mvnrepository.com/artifact/com.microsoft.azure/azure). A Maven-projektek esetében ezt a kódrészletet a `dependencies` projekt Pom-fájljának szakaszába is hozzáadhatja:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure</artifactId>
        <version>1.23.0</version>
    </dependency>
    ```

2. Használja az `AppServiceMSICredentials` objektumot a hitelesítéshez. Ez a példa azt mutatja be, hogyan használható ez a mechanizmus a Azure Key Vaulthoz való munkavégzéshez:

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

A rendszer által hozzárendelt identitást eltávolíthatja a szolgáltatás a portál, a PowerShell vagy a parancssori felület használatával történő letiltásával ugyanúgy, ahogyan azt létrehozták. A felhasználó által hozzárendelt identitások egyenként eltávolíthatók. Az összes identitás eltávolításához állítsa az identitás típusát "None" értékre.

A rendszer által hozzárendelt identitások eltávolítása az Azure AD-ből is törölve lesz. A rendszer által hozzárendelt identitások is automatikusan törlődnek az Azure AD-ből az alkalmazás-erőforrás törlésekor.

Az [ARM-sablonban](#using-an-azure-resource-manager-template)lévő összes identitás eltávolítása:

```json
"identity": {
    "type": "None"
}
```

Azure PowerShell összes identitásának eltávolítása (csak Azure Functions):

```azurepowershell-interactive
# Update an existing function app to have IdentityType "None".
Update-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -IdentityType None
```

> [!NOTE]
> Van olyan Alkalmazásbeállítás is, amely beállítható, WEBSITE_DISABLE_MSI, amely egyszerűen letiltja a helyi jogkivonat-szolgáltatást. Azonban elhagyja az identitást, és az eszközök továbbra is a felügyelt identitást "be" vagy "engedélyezve" állapotba helyezik. Ennek eredményeképpen a beállítás használata nem ajánlott.

## <a name="next-steps"></a>Következő lépések

- [Biztonságos hozzáférés SQL Database felügyelt identitás használatával](app-service-web-tutorial-connect-msi.md)
- [Az Azure Storage biztonságos elérése felügyelt identitás használatával](scenario-secure-app-access-storage.md)
- [Microsoft Graph biztonságos hívása felügyelt identitás használatával](scenario-secure-app-access-microsoft-graph-as-app.md)

[Microsoft. Azure. Services. AppAuthentication – dokumentáció]: /dotnet/api/overview/azure/service-to-service-authentication
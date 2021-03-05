---
title: Hitelesítés beállítása
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan állíthatja be és konfigurálhatja a hitelesítési módszert a Azure Machine Learning különböző erőforrásaihoz és munkafolyamataihoz.
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 11/05/2020
ms.topic: conceptual
ms.custom: how-to, has-adal-ref, devx-track-js, devx-track-azurecli, contperf-fy21q2
ms.openlocfilehash: 30e4fede72df8eaf922745e7781c9e0d11f7ddb4
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102210818"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Hitelesítés beállítása az Azure Machine Learning-erőforrásokhoz és -munkafolyamatokhoz


Megtudhatja, hogyan állíthatja be a hitelesítést a Azure Machine Learning munkaterületen. A Azure Machine Learning munkaterület hitelesítése a legtöbb esetben __Azure Active Directory__ (Azure ad) alapul. Általánosságban elmondható, hogy három hitelesítési munkafolyamat használható a munkaterülethez való csatlakozáskor:

* __Interaktív__: a fiókját a Azure Active Directory közvetlenül a hitelesítéshez, vagy a hitelesítéshez használt token beszerzéséhez használja. Az interaktív hitelesítés a _kísérletezés és az ismétlődő fejlesztés_ során használatos. Az interaktív hitelesítés lehetővé teszi az erőforrásokhoz (például webszolgáltatásokhoz) való hozzáférés szabályozását felhasználónkénti alapon.

* __Egyszerű szolgáltatásnév__: egyszerű szolgáltatásnév-fiókot hoz létre a Azure Active Directoryban, és a hitelesítéshez vagy a jogkivonat lekéréséhez használja azt. Az egyszerű szolgáltatásnév akkor használatos, ha automatikus folyamatra van szükség a szolgáltatásban _való hitelesítéshez_ anélkül, hogy felhasználói beavatkozásra lenne szükség. Például egy folyamatos integrációs és üzembe helyezési parancsfájl, amely a modell minden egyes változásakor betanítja és teszteli a modellt.

* __Felügyelt identitás__: ha az Azure Machine learning SDK-t _egy Azure-beli virtuális gépen_ használja, felügyelt identitást használhat az Azure-hoz. Ez a munkafolyamat lehetővé teszi, hogy a virtuális gép a felügyelt identitás használatával kapcsolódjon a munkaterülethez a hitelesítő adatoknak a Python-kódban való tárolása, illetve a felhasználó hitelesítésének megkérdezése nélkül. A Azure Machine Learning számítási fürtök úgy is konfigurálhatók, hogy felügyelt identitást használjanak a munkaterülethez való hozzáféréshez a _modellek betanításakor_.

> [!IMPORTANT]
> A használt hitelesítési munkafolyamattól függetlenül az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) az erőforrások számára engedélyezett hozzáférési szint (Engedélyezés) hatókörére szolgál. Előfordulhat például, hogy egy rendszergazda vagy automatizálási folyamat hozzáfér egy számítási példány létrehozásához, de nem használja azt, míg egy adattudós felhasználhatja, de nem törölheti vagy létrehozhatja. További információt a [Azure Machine learning munkaterület hozzáférésének kezelése](how-to-assign-roles.md)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

* Hozzon létre egy [Azure Machine learning munkaterületet](how-to-manage-workspace.md).
* [Állítsa be a fejlesztési környezetet](how-to-configure-environment.md) a Azure Machine learning SDK telepítéséhez, vagy használjon egy [Azure Machine learning számítási példányt](concept-azure-machine-learning-architecture.md#compute-instance) az SDK-val, amely már telepítve van.

## <a name="azure-active-directory"></a>Azure Active Directory

A munkaterület összes hitelesítési munkafolyamata a Azure Active Directoryra támaszkodik. Ha azt szeretné, hogy a felhasználók az egyes fiókokat használva hitelesítsék magukat, az Azure AD-ben fiókokkal kell rendelkezniük. Ha egyszerű szolgáltatásokat szeretne használni, akkor az Azure AD-ben léteznie kell. A felügyelt identitások az Azure AD egyik funkciója is. 

További információ az Azure AD-ről: [Mi az Azure Active Directory hitelesítés](..//active-directory/authentication/overview-authentication.md).

Miután létrehozta az Azure AD-fiókokat, tekintse meg a [Azure Machine learning munkaterület hozzáférésének kezelése](how-to-assign-roles.md) című témakört, amely tájékoztatást nyújt számukra a munkaterülethez való hozzáféréshez és a Azure Machine learning egyéb műveleteihez.

## <a name="configure-a-service-principal"></a>Egyszerű szolgáltatás konfigurálása

Egyszerű szolgáltatásnév (SP) használatához először létre kell hoznia az SP-t, és hozzáférést kell biztosítania a munkaterülethez. Ahogy azt korábban említettük, az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával szabályozható a hozzáférés, ezért azt is el kell döntenie, hogy milyen hozzáférést biztosít az SP számára.

> [!IMPORTANT]
> Egyszerű szolgáltatásnév használata esetén adja meg a által használt __feladathoz szükséges minimális hozzáférést__ . Előfordulhat például, hogy nem ad meg egy egyszerű szolgáltatás tulajdonosának vagy közreműködői hozzáférését, ha az az összes használatban van a webes telepítés hozzáférési jogkivonatának beolvasásához.
>
> A legkevesebb hozzáférés megadásának oka az, hogy egy egyszerű szolgáltatásnév jelszót használ a hitelesítéshez, és a jelszót egy Automation-parancsfájl részeként lehet tárolni. Ha a jelszó kiszivárgott, az adott feladatokhoz szükséges minimális hozzáférés minimálisra csökken az SP rosszindulatú használata esetén.

Az [Azure parancssori](/cli/azure/install-azure-cli)felületének használatával a legkönnyebben hozhat létre egy SP-t, és hozzáférést biztosíthat a munkaterületéhez. Egyszerű szolgáltatásnév létrehozásához és a munkaterülethez való hozzáférés biztosításához kövesse az alábbi lépéseket:

> [!NOTE]
> Ezen lépések végrehajtásához rendszergazdai jogosultsággal kell rendelkeznie az előfizetésben.

1. Hitelesítés az Azure-előfizetésében:

    ```azurecli-interactive
    az login
    ```

    Ha a CLI megnyithatja az alapértelmezett böngészőt, akkor megnyitja, és betölti a bejelentkezési oldalt. Ellenkező esetben meg kell nyitnia egy böngészőt, és követnie kell a parancssor utasításait. Az utasítások [https://aka.ms/devicelogin](https://aka.ms/devicelogin) egy engedélyezési kód böngészését és beírását foglalják magukban.

    Ha több Azure-előfizetéssel rendelkezik, akkor a `az account set -s <subscription name or ID>` paranccsal állíthatja be az előfizetést. További információ: [több Azure-előfizetés használata](/cli/azure/manage-azure-subscriptions-azure-cli).

    A hitelesítés egyéb módszereivel kapcsolatban lásd: [Bejelentkezés az Azure CLI-vel](/cli/azure/authenticate-azure-cli).

1. Telepítse a Azure Machine Learning bővítményt:

    ```azurecli-interactive
    az extension add -n azure-cli-ml
    ```

1. Hozza létre az egyszerű szolgáltatásnevet. A következő példában egy **ml-Auth** nevű SP jön létre:

    ```azurecli-interactive
    az ad sp create-for-rbac --sdk-auth --name ml-auth
    ```

    A kimenet az alábbihoz hasonló JSON lesz. Jegyezze fel a `clientId` , `clientSecret` és a `tenantId` mezőket, ahogy a cikk más lépéseire is szüksége lesz.

    ```json
    {
        "clientId": "your-client-id",
        "clientSecret": "your-client-secret",
        "subscriptionId": "your-sub-id",
        "tenantId": "your-tenant-id",
        "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
        "resourceManagerEndpointUrl": "https://management.azure.com",
        "activeDirectoryGraphResourceId": "https://graph.windows.net",
        "sqlManagementEndpointUrl": "https://management.core.windows.net:5555",
        "galleryEndpointUrl": "https://gallery.azure.com/",
        "managementEndpointUrl": "https://management.core.windows.net"
    }
    ```

1. Kérje le az egyszerű szolgáltatásnév adatait az `clientId` előző lépésben visszaadott érték használatával:

    ```azurecli-interactive
    az ad sp show --id your-client-id
    ```

    A következő JSON a parancs kimenetének egyszerűsített példája. Jegyezze fel a `objectId` mezőt, mivel a következő lépéshez szükséges értéknek kell lennie.

    ```json
    {
        "accountEnabled": "True",
        "addIns": [],
        "appDisplayName": "ml-auth",
        ...
        ...
        ...
        "objectId": "your-sp-object-id",
        "objectType": "ServicePrincipal"
    }
    ```

1. Engedélyezi az SP számára a Azure Machine Learning munkaterület elérését. Szüksége lesz a munkaterület nevére és az erőforráscsoport nevére a és a paraméterek számára `-w` `-g` . A `--user` paraméter esetében használja az `objectId` előző lépésben megadott értéket. A `--role` paraméter lehetővé teszi az egyszerű szolgáltatásnév hozzáférési szerepkörének beállítását. A következő példában az SP a **tulajdonosi** szerepkörhöz van rendelve. 

    > [!IMPORTANT]
    > A tulajdonosi hozzáférés lehetővé teszi, hogy az egyszerű szolgáltatás gyakorlatilag bármilyen műveletet hajtson végre a munkaterületen. Ez a dokumentum a hozzáférés megadásának bemutatására szolgál. éles környezetben a Microsoft azt javasolja, hogy a szolgáltatásnak a szükséges szerepkör elvégzéséhez szükséges minimális hozzáférést adja meg. A forgatókönyvhöz szükséges hozzáféréssel rendelkező egyéni szerepkör létrehozásával kapcsolatos információkért lásd: [Azure Machine learning munkaterület hozzáférésének kezelése](how-to-assign-roles.md).

    ```azurecli-interactive
    az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
    ```

    Ez a hívás nem eredményez sikeres kimenetet.

## <a name="configure-a-managed-identity"></a>Felügyelt identitás konfigurálása

> [!IMPORTANT]
> A felügyelt identitás csak akkor támogatott, ha az Azure Machine Learning SDK-t egy Azure-beli virtuális gépről vagy egy Azure Machine Learning számítási fürtből használja. A felügyelt identitások számítási fürttel való használata jelenleg előzetes verzióban érhető el.

### <a name="managed-identity-with-a-vm"></a>Felügyelt identitás virtuális géppel

1. [Az Azure-erőforrások rendszerhez rendelt felügyelt identitásának engedélyezése a virtuális gépen](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity).

1. A [Azure Portal](https://portal.azure.com)válassza ki a munkaterületet, majd válassza a __Access Control (iam)__ lehetőséget, __adja hozzá a szerepkör-hozzárendelést__, majd válassza a __virtuális gép__ lehetőséget a __hozzáférés hozzárendelése__ legördülő listához. Végül válassza ki a virtuális gép identitását.

1. Válassza ki az identitáshoz rendelni kívánt szerepkört. Például a közreműködő vagy egy egyéni szerepkör. További információ: az [erőforrásokhoz való hozzáférés szabályozása](how-to-assign-roles.md).

### <a name="managed-identity-with-compute-cluster"></a>Felügyelt identitás számítási fürttel

További információ: a [felügyelt identitás beállítása a számítási fürthöz](how-to-create-attach-compute-cluster.md#managed-identity).

<a id="interactive-authentication"></a>

## <a name="use-interactive-authentication"></a>Interaktív hitelesítés használata

> [!IMPORTANT]
> Az interaktív hitelesítés a böngészőt használja, és cookie-kat igényel (beleértve a harmadik féltől származó cookie-kat is). Ha letiltotta a cookie-kat, a következő hibaüzenet jelenhet meg: "nem sikerült bejelentkezni." Ez a hiba akkor is előfordulhat, ha engedélyezte az [Azure ad-multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md).

A dokumentációban és a mintákban a legtöbb példa interaktív hitelesítést használ. Az SDK használatakor például két függvényhívás van, amely automatikusan rákérdez a felhasználói felületen alapuló hitelesítési folyamatra:

* A függvény meghívásakor `from_config()` a rendszer kiadja a kérdést.

    ```python
    from azureml.core import Workspace
    ws = Workspace.from_config()
    ```

    A `from_config()` függvény egy JSON-fájlt keres, amely a munkaterülethez való csatlakozáshoz szükséges információkat tartalmazza.

* Ha a `Workspace` konstruktort használja az előfizetés, az erőforráscsoport és a munkaterület adatainak megadására, a rendszer az interaktív hitelesítést is kéri.

    ```python
    ws = Workspace(subscription_id="your-sub-id",
                  resource_group="your-resource-group-id",
                  workspace_name="your-workspace-name"
                  )
    ```

> [!TIP]
> Ha több bérlőhöz fér hozzá, előfordulhat, hogy importálnia kell az osztályt, és explicit módon meg kell határoznia, hogy melyik bérlőt célozza meg. A konstruktor meghívása azt `InteractiveLoginAuthentication` is kéri, hogy a fenti hívásokhoz hasonló módon jelentkezzen be.
>
> ```python
> from azureml.core.authentication import InteractiveLoginAuthentication
> interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
> ```

Az Azure CLI használatakor a `az login` parancs a CLI-munkamenet hitelesítésére szolgál. További információ: Ismerkedés [Az Azure CLI-vel](/cli/azure/get-started-with-azure-cli).

> [!TIP]
> Ha az SDK-t olyan környezetből használja, amelyben korábban már hitelesítette az Azure CLI-t, az `AzureCliAuthentication` osztály használatával hitelesítheti a munkaterületet a CLI által gyorsítótárazott hitelesítő adatok használatával:
>
> ```python
> from azureml.core.authentication import AzureCliAuthentication
> cli_auth = AzureCliAuthentication()
> ws = Workspace(subscription_id="your-sub-id",
>                resource_group="your-resource-group-id",
>                workspace_name="your-workspace-name",
>                auth=cli_auth
>                )
> ```

<a id="service-principal-authentication"></a>

## <a name="use-service-principal-authentication"></a>Egyszerű szolgáltatás hitelesítésének használata

Ha az SDK-ban szeretné hitelesíteni a munkaterületet, az egyszerű szolgáltatásnév használatával használja az `ServicePrincipalAuthentication` osztály konstruktorát. Használja a szolgáltató paraméterként való létrehozásakor kapott értékeket. A `tenant_id` paraméter leképezi a `tenantId` fenti, a `service_principal_id` és a `clientId` leképezését a következőre: `service_principal_password` `clientSecret` .

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

A `sp` változó most már rendelkezik egy olyan hitelesítési objektummal, amelyet közvetlenül az SDK-ban használ. Általánosságban elmondható, hogy a fent használt azonosítókat/titkokat a következő kódban látható módon tárolja a környezeti változókon. A környezeti változókban való tárolás megakadályozza, hogy az adatok véletlenül bekerüljenek egy GitHub-tárházba.

```python
import os

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

A Pythonban futó és az SDK-t használó automatizált munkafolyamatok esetében az adott objektumot a legtöbb esetben használhatja a hitelesítéshez. A következő kód a létrehozott Auth objektum használatával hitelesíti a munkaterületet.

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example",
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

### <a name="use-a-service-principal-from-the-azure-cli"></a>Egyszerű szolgáltatásnév használata az Azure CLI-vel

Az Azure CLI-parancsokhoz használhat egyszerű szolgáltatást. További információ: [Bejelentkezés egyszerű szolgáltatásnév használatával](/cli/azure/create-an-azure-service-principal-azure-cli#sign-in-using-a-service-principal).

### <a name="use-a-service-principal-with-the-rest-api-preview"></a>Egyszerű szolgáltatásnév használata a REST API (előzetes verzió)

Az egyszerű szolgáltatásnév a Azure Machine Learning [REST API](/rest/api/azureml/) (előzetes verzió) hitelesítésére is használható. A Azure Active Directory ügyfél- [hitelesítő adatok engedélyezési folyamatát](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)használja, amely lehetővé teszi, hogy a szolgáltatások közötti hívás a fej nélküli hitelesítéshez automatizált munkafolyamatokban történjen. A példák a Python és a Node.js [ADAL-függvénytárával](../active-directory/azuread-dev/active-directory-authentication-libraries.md) valósulnak meg, de az OpenID Connect 1,0-et támogató nyílt forráskódú kódtár is használható.

> [!NOTE]
> MSAL.js egy újabb könyvtár, mint a ADAL, de az ügyfél hitelesítő adataival nem végezhető el a szolgáltatás és a szolgáltatás közötti hitelesítés MSAL.js, mivel ez elsősorban olyan ügyféloldali függvénytár, amely egy adott felhasználóhoz kötött interaktív/felhasználói felületi hitelesítéshez készült. Javasoljuk, hogy az alábbi módon használja az ADAL-t az automatizált munkafolyamatok létrehozásához a REST API használatával.

#### <a name="nodejs"></a>Node.js

A következő lépésekkel hozhatja Node.js használatával a hitelesítési tokent. A környezetben futtassa a parancsot `npm install adal-node` . Ezután használja a, `tenantId` a `clientId` és a `clientSecret` parancsot a fenti lépésekben létrehozott szolgáltatásnév alapján a következő parancsfájlban található egyező változók értékeiként.

```javascript
const adal = require('adal-node').AuthenticationContext;

const authorityHostUrl = 'https://login.microsoftonline.com/';
const tenantId = 'your-tenant-id';
const authorityUrl = authorityHostUrl + tenantId;
const clientId = 'your-client-id';
const clientSecret = 'your-client-secret';
const resource = 'https://management.azure.com/';

const context = new adal(authorityUrl);

context.acquireTokenWithClientCredentials(
  resource,
  clientId,
  clientSecret,
  (err, tokenResponse) => {
    if (err) {
      console.log(`Token generation failed due to ${err}`);
    } else {
      console.dir(tokenResponse, { depth: null, colors: true });
    }
  }
);
```

A változó `tokenResponse` egy olyan objektum, amely tartalmazza a jogkivonatot és a társított metaadatokat, például a lejárati időt. A tokenek 1 órára érvényesek, és az új jogkivonat lekéréséhez futtassa újra ugyanezt a hívást. A következő kódrészlet egy mintául szolgáló válasz.

```javascript
{
    tokenType: 'Bearer',
    expiresIn: 3599,
    expiresOn: 2019-12-17T19:15:56.326Z,
    resource: 'https://management.azure.com/',
    accessToken: "random-oauth-token",
    isMRRT: true,
    _clientId: 'your-client-id',
    _authority: 'https://login.microsoftonline.com/your-tenant-id'
}
```

Az `accessToken` Auth token beolvasásához használja a tulajdonságot. Tekintse meg a [REST API dokumentációját](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) , amely bemutatja, hogyan használhatja a tokent API-hívások létrehozásához.

#### <a name="python"></a>Python

A következő lépésekkel hozhatja végre a hitelesítési tokent a Python használatával. A környezetben futtassa a parancsot `pip install adal` . Ezután használja a `tenantId` , a `clientId` és a `clientSecret` parancsot a fenti lépésekben a következő parancsfájlban a megfelelő változók értékeiként létrehozott egyszerű szolgáltatásnév alapján.

```python
from adal import AuthenticationContext

client_id = "your-client-id"
client_secret = "your-client-secret"
resource_url = "https://login.microsoftonline.com"
tenant_id = "your-tenant-id"
authority = "{}/{}".format(resource_url, tenant_id)

auth_context = AuthenticationContext(authority)
token_response = auth_context.acquire_token_with_client_credentials("https://management.azure.com/", client_id, client_secret)
print(token_response)
```

A változó `token_response` egy olyan szótár, amely tartalmazza a tokent és a kapcsolódó metaadatokat, például a lejárati időt. A tokenek 1 órára érvényesek, és az új jogkivonat lekéréséhez futtassa újra ugyanezt a hívást. A következő kódrészlet egy mintául szolgáló válasz.

```python
{
    'tokenType': 'Bearer',
    'expiresIn': 3599,
    'expiresOn': '2019-12-17 19:47:15.150205',
    'resource': 'https://management.azure.com/',
    'accessToken': 'random-oauth-token',
    'isMRRT': True,
    '_clientId': 'your-client-id',
    '_authority': 'https://login.microsoftonline.com/your-tenant-id'
}
```

`token_response["accessToken"]`Az hitelesítési jogkivonat beolvasására használatos. Tekintse meg a [REST API dokumentációját](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) , amely bemutatja, hogyan használhatja a tokent API-hívások létrehozásához.

#### <a name="java"></a>Java

A Java-ban kérje le a tulajdonosi jogkivonatot standard REST-hívás használatával:

```java
String tenantId = "your-tenant-id";
String clientId = "your-client-id";
String clientSecret = "your-client-secret";
String resourceManagerUrl = "https://management.azure.com";

HttpRequest tokenAuthenticationRequest = tokenAuthenticationRequest(tenantId, clientId, clientSecret, resourceManagerUrl);

HttpClient client = HttpClient.newBuilder().build();
Gson gson = new Gson();
HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
if (response.statusCode == 200)
{
     body = gson.fromJson(body, AuthenticationBody.class);

    // ... etc ... 
}
// ... etc ...

static HttpRequest tokenAuthenticationRequest(String tenantId, String clientId, String clientSecret, String resourceManagerUrl){
    String authUrl = String.format("https://login.microsoftonline.com/%s/oauth2/token", tenantId);
    String clientIdParam = String.format("client_id=%s", clientId);
    String resourceParam = String.format("resource=%s", resourceManagerUrl);
    String clientSecretParam = String.format("client_secret=%s", clientSecret);

    String bodyString = String.format("grant_type=client_credentials&%s&%s&%s", clientIdParam, resourceParam, clientSecretParam);

    HttpRequest request = HttpRequest.newBuilder()
            .uri(URI.create(authUrl))
            .POST(HttpRequest.BodyPublishers.ofString(bodyString))
            .build();
    return request;
}

class AuthenticationBody {
    String access_token;
    String token_type;
    int expires_in;
    String scope;
    String refresh_token;
    String id_token;
    
    AuthenticationBody() {}
}
```

Az előző kódnak a-től eltérő kivételeket és állapotkódot kell kezelnie `200 OK` , de a következő mintát mutatja: 

- Az ügyfél-azonosító és a titkos kulcs használatával ellenőrizze, hogy a programnak rendelkeznie kell-e hozzáféréssel
- A bérlői azonosító használata a keresett hely megadásához `login.microsoftonline.com`
- Azure Resource Manager használata az engedélyezési jogkivonat forrásaként

## <a name="use-managed-identity-authentication"></a>Felügyelt identitások hitelesítésének használata

Ha felügyelt identitással konfigurált virtuális gépről vagy számítási fürtről szeretne hitelesítést végezni a munkaterületen, használja a `MsiAuthentication` osztályt. Az alábbi példa bemutatja, hogyan használhatja ezt az osztályt egy munkaterületre történő hitelesítéshez:

```python
from azureml.core.authentication import MsiAuthentication

msi_auth = MsiAuthentication()

ws = Workspace(subscription_id="your-sub-id",
                resource_group="your-resource-group-id",
                workspace_name="your-workspace-name",
                auth=msi_auth
                )
```

## <a name="next-steps"></a>Következő lépések

* A [titkok használata a képzésben](how-to-use-secrets-in-runs.md).
* [Webszolgáltatásként üzembe helyezett modellek hitelesítésének konfigurálása](how-to-authenticate-web-service.md).
* [Azure Machine Learning-modell felhasználása webszolgáltatásként](how-to-consume-web-service.md).

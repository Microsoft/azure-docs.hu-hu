---
title: Azure Batch szolgáltatások hitelesítése Azure Active Directory
description: A Batch támogatja az Azure AD-t a Batch szolgáltatásban történő hitelesítéshez. Megtudhatja, hogyan végezheti el a hitelesítést két módszer egyikével.
ms.topic: how-to
ms.date: 10/20/2020
ms.custom: has-adal-ref
ms.openlocfilehash: 2ceefa538c44208750da8986fcf3d161f0c0865f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102180020"
---
# <a name="authenticate-batch-service-solutions-with-active-directory"></a>Batch szolgáltatási megoldások hitelesítése Active Directory

Azure Batch támogatja a [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure ad) hitelesítését. Az Azure AD a Microsoft több-bérlős felhőalapú címtár-és Identitáskezelés-kezelő szolgáltatása. Az Azure maga az Azure AD-t használja az ügyfelek, a szolgáltatás-rendszergazdák és a szervezeti felhasználók hitelesítéséhez.

Az Azure AD-hitelesítés Azure Batch használatával történő használatakor kétféleképpen végezheti el a hitelesítést:

- **Integrált hitelesítéssel** hitelesítheti az alkalmazással kommunikáló felhasználókat. Az integrált hitelesítést használó alkalmazások összegyűjtik a felhasználó hitelesítő adatait, és ezeket a hitelesítő adatokat használják a Batch-erőforrásokhoz való hozzáférés hitelesítéséhez.
- Egy **egyszerű szolgáltatásnév** használatával hitelesítheti a felügyelet nélküli alkalmazást. Egy egyszerű szolgáltatásnév határozza meg az alkalmazáshoz tartozó házirendet és engedélyeket, hogy az alkalmazás az erőforrásokhoz való hozzáféréskor a futtatókörnyezetben legyen elérhető.

Az Azure AD-vel kapcsolatos további tudnivalókért tekintse meg a [Azure Active Directory dokumentációját](../active-directory/index.yml).

## <a name="endpoints-for-authentication"></a>Hitelesítéshez használt végpontok

A Batch-alkalmazások Azure AD-vel történő hitelesítéséhez néhány jól ismert végpontot kell tartalmaznia a kódban.

### <a name="azure-ad-endpoint"></a>Azure AD-végpont

Az alapszintű Azure AD-szolgáltató végpontja:

`https://login.microsoftonline.com/`

Az Azure AD-vel való hitelesítéshez ezt a végpontot kell használni a bérlői AZONOSÍTÓval (a címtár-AZONOSÍTÓval) együtt. A bérlő azonosítója azonosítja a hitelesítéshez használandó Azure AD-bérlőt. A bérlő AZONOSÍTÓjának lekéréséhez kövesse a [Azure Active Directory bérlői azonosítójának lekérése](#get-the-tenant-id-for-your-active-directory)című szakasz lépéseit:

`https://login.microsoftonline.com/<tenant-id>`

> [!NOTE]
> A bérlő-specifikus végpontra akkor van szükség, ha egy egyszerű szolgáltatásnév használatával végzi a hitelesítést.
>
> A bérlő-specifikus végpont nem kötelező, ha az integrált hitelesítés használatával végzi a hitelesítést, de ajánlott. Azonban használhatja az Azure AD Common Endpoint (általános) végpontját is. A Common Endpoint (általános hitelesítő adatok összegyűjtése) felületet biztosít, ha nincs megadva egy adott bérlő. Az általános végpont `https://login.microsoftonline.com/common` .

További információ az Azure AD-végpontokról: [hitelesítés és engedélyezés](../active-directory/develop/authentication-vs-authorization.md).

### <a name="batch-resource-endpoint"></a>Batch erőforrás-végpont

A **Azure batch erőforrás-végpont** használatával szerezzen be egy jogkivonatot a kérelmeknek a Batch szolgáltatásba történő hitelesítéséhez:

`https://batch.core.windows.net/`

## <a name="register-your-application-with-a-tenant"></a>Alkalmazás regisztrálása Bérlővel

Az Azure AD hitelesítéshez való használatának első lépése az alkalmazás regisztrálása egy Azure AD-bérlőben. Az alkalmazás regisztrálása lehetővé teszi, hogy meghívja az Azure [Active Directory-hitelesítési tár](../active-directory/azuread-dev/active-directory-authentication-libraries.md) (ADAL) a kódból. A ADAL API-t biztosít az Azure AD-vel való hitelesítéshez az alkalmazásból. Az alkalmazás regisztrálása kötelező, függetlenül attól, hogy integrált hitelesítést vagy egyszerű szolgáltatásnevet kíván használni.

Az alkalmazás regisztrálásakor az Azure AD-vel kapcsolatos információkat biztosít az alkalmazásról. Az Azure AD ezt követően egy alkalmazás-azonosítót (más néven *ügyfél-azonosítót*) biztosít az alkalmazás Azure ad-hez való hozzárendeléséhez futásidőben. Az alkalmazás-AZONOSÍTÓval kapcsolatos további tudnivalókért tekintse meg az [alkalmazás-és szolgáltatásnév objektumait Azure Active Directoryban](../active-directory/develop/app-objects-and-service-principals.md).

A Batch-alkalmazás regisztrálásához kövesse az alkalmazás **regisztrálása** című rövid útmutatóban ismertetett lépéseket [: alkalmazás regisztrálása a Microsoft Identity platformon](../active-directory/develop/quickstart-register-app.md). Ha natív alkalmazásként regisztrálja az alkalmazást, megadhat bármely érvényes URI-t az **átirányítási URI**-hoz. Nem kell valódi végpontnak lennie.

Az alkalmazás regisztrálását követően megjelenik az alkalmazás azonosítója:

![A Azure Portalban megjelenő alkalmazás-azonosító képernyőképe.](./media/batch-aad-auth/app-registration-data-plane.png)

## <a name="get-the-tenant-id-for-your-active-directory"></a>A Active Directory bérlői AZONOSÍTÓjának beolvasása

A bérlő azonosítója azonosítja azt az Azure AD-bérlőt, amely hitelesítési szolgáltatásokat biztosít az alkalmazás számára. A bérlő AZONOSÍTÓjának lekéréséhez kövesse az alábbi lépéseket:

1. A Azure Portal válassza ki a Active Directory.
1. Válassza ki a **Tulajdonságok** elemet.
1. Másolja a **címtár-azonosítóhoz** megadott GUID értéket. Ezt az értéket a bérlői AZONOSÍTÓnak is nevezik.

![Képernyőkép a Azure Portal található címtár-AZONOSÍTÓról.](./media/batch-aad-auth/aad-directory-id.png)

## <a name="use-integrated-authentication"></a>Integrált hitelesítés használata

Az integrált hitelesítéssel történő hitelesítéshez meg kell adnia az alkalmazás engedélyeit a Batch szolgáltatás API-hoz való kapcsolódáshoz. Ez a lépés lehetővé teszi, hogy az alkalmazás a Batch szolgáltatás API-ját az Azure AD-vel hitelesítse.

Miután regisztrálta az alkalmazást, kövesse az alábbi lépéseket a Azure Portal, hogy hozzáférést biztosítson a Batch szolgáltatáshoz:

1. A Azure Portal válassza a **minden szolgáltatás** lehetőséget, majd válassza az **alkalmazás-regisztrációk** lehetőséget.
1. Keresse meg az alkalmazás nevét az alkalmazások regisztrációinak listájában.
1. Válassza ki az alkalmazást, és válassza az **API-engedélyek** lehetőséget.
1. Az **API-engedélyek** szakaszban válassza az **engedély hozzáadása** elemet.
1. Az **API kiválasztása lapon** keressen rá a Batch API kifejezésre. Keressen rá az egyes sztringekre, addig amíg meg nem találja az API-t:
    1. **Microsoft Azure Batch**
    1. A **ddbf3205-c6bd-46ae-8127-60eb93363864** a Batch API azonosítója.
1. Miután megtalálta a Batch API-t, jelölje ki, majd válassza a **kiválasztás** lehetőséget.
1. Az **engedélyek kiválasztása** területen jelölje be a **hozzáférés Azure batch szolgáltatás** melletti jelölőnégyzetet, majd válassza az **engedélyek hozzáadása** elemet.

Az **API-engedélyek** szakasz azt mutatja, hogy az Azure ad-alkalmazás a Microsoft Graph és a Batch szolgáltatás API-ját is elérheti. Az Azure AD-vel való első regisztráláskor az engedélyek Microsoft Graph automatikusan megadhatók.

## <a name="use-a-service-principal"></a>Egyszerű szolgáltatásnév használata

Felügyelet nélküli alkalmazást futtató alkalmazás hitelesítéséhez használjon egyszerű szolgáltatásnevet. Az alkalmazás regisztrálása után kövesse az alábbi lépéseket a Azure Portal egy egyszerű szolgáltatásnév konfigurálásához:

1. Kérjen titkos kulcsot az alkalmazáshoz.
1. Rendeljen Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) az alkalmazáshoz.

### <a name="request-a-secret-for-your-application"></a>Titkos kód kérése az alkalmazáshoz

Ha az alkalmazás egy egyszerű szolgáltatással végzi a hitelesítést, az az alkalmazás AZONOSÍTÓját és az Azure AD titkos kulcsát is elküldi. Létre kell hoznia és át kell másolnia a kód alapján használni kívánt titkos kulcsot.

Hajtsa végre a következő lépéseket az Azure Portalon:

1. A Azure Portal válassza a **minden szolgáltatás** lehetőséget. Válassza az **alkalmazás-regisztrációk** lehetőséget.
1. Válassza ki az alkalmazást az alkalmazás-regisztrációk listájából.
1. Válassza ki az alkalmazást, majd válassza a **tanúsítványok & Secrets** elemet. Az **ügyfél titkai** szakaszban válassza az **új ügyfél titka** elemet.
1. Titkos kód létrehozásához adja meg a titok leírását. Ezután válasszon ki egy évet, két évet vagy nincs lejáratot a titkos kulcshoz.
1. A titok létrehozásához és megjelenítéséhez válassza a **Hozzáadás** lehetőséget. Másolja a titkos értéket egy biztonságos helyre, mivel az oldal elhagyása után többé nem fog tudni hozzáférni.

### <a name="assign-azure-rbac-to-your-application"></a>Azure-RBAC kiosztása az alkalmazáshoz

Az egyszerű szolgáltatással történő hitelesítéshez Azure-RBAC kell rendelnie az alkalmazáshoz. Kövesse az alábbi lépéseket:

1. A Azure Portal navigáljon az alkalmazás által használt batch-fiókhoz.
1. A Batch-fiók **Beállítások** szakaszában válassza a **Access Control (iam)** lehetőséget.
1. Válassza ki a **szerepkör-hozzárendelések** lapot.
1. Válassza a **Szerepkör-hozzárendelés hozzáadása** lehetőséget.
1. A **szerepkör** legördülő listából válassza ki az alkalmazás *közreműködő* vagy *olvasó* szerepkörét. További információ ezekről a szerepkörökről: [Az Azure szerepköralapú hozzáférés-vezérlésének első lépései a Azure Portal](../role-based-access-control/overview.md).
1. A **Select (kiválasztás** ) mezőben adja meg az alkalmazás nevét. Válassza ki az alkalmazást a listából, majd válassza a **Mentés** lehetőséget.

Az alkalmazásnak ekkor meg kell jelennie a hozzáférés-vezérlési beállításaiban egy hozzárendelt Azure-szerepkörrel.

![Azure-szerepkör kiosztása az alkalmazáshoz](./media/batch-aad-auth/app-rbac-role.png)

### <a name="assign-a-custom-role"></a>Egyéni szerepkör hozzárendelése

Az egyéni szerepkörök részletes engedélyeket biztosítanak a felhasználóknak a feladatok, feladatok és egyebek elküldéséhez. Ezzel megakadályozható, hogy a felhasználók a költségeket befolyásoló műveleteket végezzenek, például készletek létrehozását vagy csomópontok módosítását.

A következő Azure RBAC-műveletekhez egyéni szerepkörrel adhat engedélyeket egy Azure AD-felhasználónak,-csoportnak vagy egyszerű szolgáltatásnév számára:

- Microsoft.BatCH/batchAccounts/készletek/írás
- Microsoft.BatCH/batchAccounts/készletek/törlés
- Microsoft.BatCH/batchAccounts/készletek/olvasás
- Microsoft.BatCH/batchAccounts/jobSchedules/Write
- Microsoft.BatCH/batchAccounts/jobSchedules/delete
- Microsoft.BatCH/batchAccounts/jobSchedules/READ
- Microsoft.BatCH/batchAccounts/feladatok/írás
- Microsoft.BatCH/batchAccounts/feladatok/törlés
- Microsoft.BatCH/batchAccounts/feladatok/olvasás
- Microsoft.BatCH/batchAccounts/tanúsítványok/írás
- Microsoft.BatCH/batchAccounts/tanúsítványok/törlés
- Microsoft.BatCH/batchAccounts/tanúsítványok/olvasás
- Microsoft.BatCH/batchAccounts/Read (bármilyen olvasási művelethez)
- Microsoft.BatCH/batchAccounts/Listkeys műveletének beolvasása/Action (bármilyen művelethez)

Az egyéni szerepkörök az Azure AD által hitelesített felhasználókra vonatkoznak, nem a Batch-fiók hitelesítő adataival (megosztott kulcs). Vegye figyelembe, hogy a Batch-fiók hitelesítő adatai teljes hozzáférést biztosítanak a Batch-fiókhoz. Azt is vegye figyelembe, hogy az [autopoolt](nodes-and-pools.md#autopools) használó feladatok készlet szintű engedélyeket igényelnek.

> [!NOTE]
> Bizonyos szerepkör-hozzárendeléseket meg kell adni a művelet mezőben, míg másokat meg kell adni a DataAction mezőben. További információ: [Azure erőforrás-szolgáltatói műveletek](../role-based-access-control/resource-provider-operations.md#microsoftbatch).

Íme egy példa egy egyéni szerepkör-definícióra:

```json
{
 "properties":{
    "roleName":"Azure Batch Custom Job Submitter",
    "type":"CustomRole",
    "description":"Allows a user to submit jobs to Azure Batch but not manage pools",
    "assignableScopes":[
      "/subscriptions/88888888-8888-8888-8888-888888888888"
    ],
    "permissions":[
      {
        "actions":[
          "Microsoft.Batch/*/read",
          "Microsoft.Authorization/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*",
          "Microsoft.Insights/alertRules/*"
        ],
        "notActions":[

        ],
        "dataActions":[
          "Microsoft.Batch/batchAccounts/jobs/*",
          "Microsoft.Batch/batchAccounts/jobSchedules/*"
        ],
        "notDataActions":[

        ]
      }
    ]
  }
}
```

Az egyéni szerepkörök létrehozásával kapcsolatos további információkért lásd: [Egyéni Azure-szerepkörök](../role-based-access-control/custom-roles.md).

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>A Azure Active Directory bérlői AZONOSÍTÓjának beolvasása

A bérlő azonosítója azonosítja azt az Azure AD-bérlőt, amely hitelesítési szolgáltatásokat biztosít az alkalmazás számára. A bérlő AZONOSÍTÓjának lekéréséhez kövesse az alábbi lépéseket:

1. A Azure Portal válassza ki a Active Directory.
1. Válassza ki a **Tulajdonságok** elemet.
1. Másolja a **címtár-azonosítóhoz** megadott GUID értéket. Ezt az értéket a bérlői AZONOSÍTÓnak is nevezik.

![A könyvtár AZONOSÍTÓjának másolása](./media/batch-aad-auth/aad-directory-id.png)

## <a name="code-examples"></a>Kódpéldák

Az ebben a szakaszban szereplő példák azt mutatják be, hogyan lehet hitelesíteni az Azure AD-t az integrált hitelesítéssel és egy egyszerű szolgáltatásnév használatával. A példák többsége a .NET-et használja, de a fogalmak hasonlóak más nyelvekhez.

> [!NOTE]
> Egy Azure AD-hitelesítési jogkivonat egy óra elteltével lejár. Ha hosszú élettartamú **BatchClient** objektumot használ, javasoljuk, hogy minden kérelem esetében kérjen le egy tokent a ADAL-ből, hogy mindig érvényes jogkivonat legyen.
>
> Ennek a .NET-ben való eléréséhez írjon egy metódust, amely lekéri a jogkivonatot az Azure AD-ből, és átadja ezt a metódust egy **BatchTokenCredentials** objektumnak meghatalmazottként. A delegált metódust minden, a Batch szolgáltatásnak küldött kérelemre meghívjuk, hogy érvényes jogkivonat legyen megadva. Alapértelmezés szerint a ADAL gyorsítótárazza a tokeneket, így a rendszer csak akkor kér le új jogkivonatot az Azure AD-ből, ha szükséges. További információ az Azure AD-tokenekről: [biztonsági jogkivonatok](../active-directory/develop/security-tokens.md).

### <a name="code-example-using-azure-ad-integrated-authentication-with-batch-net"></a>Példa a kód használatára: az Azure AD integrált hitelesítésének használata a Batch .NET-tel

A Batch .NET-ből származó integrált hitelesítéssel történő hitelesítéshez hivatkozzon a [Azure Batch .net](https://www.nuget.org/packages/Microsoft.Azure.Batch/) -csomagra és a [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) -csomagra.

Adja meg a következő `using` utasításokat a kódban:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Hivatkozzon a kódban található Azure AD-végpontra, beleértve a bérlő AZONOSÍTÓját. A bérlő AZONOSÍTÓjának lekéréséhez kövesse a [Azure Active Directory bérlői azonosítójának lekérése](#get-the-tenant-id-for-your-active-directory)című szakasz lépéseit:

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

A Batch szolgáltatás erőforrás-végpontjának hivatkozása:

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

A Batch-fiókra való hivatkozás:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Adja meg az alkalmazás AZONOSÍTÓját (ügyfél-AZONOSÍTÓját). Az alkalmazás-azonosító elérhető az alkalmazás regisztrációjában a Azure Portalban:

```csharp
private const string ClientId = "<application-id>";
```

Másolja a megadott átirányítási URI-t is, ha az alkalmazást natív alkalmazásként regisztrálta. A kódban megadott átirányítási URI-nek meg kell egyeznie az alkalmazás regisztrálásakor megadott átirányítási URI-azonosítóval:

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

A hitelesítési jogkivonat Azure AD-ből való beszerzéséhez írjon be egy visszahívási módszert. Az itt látható **GetAuthenticationTokenAsync** callback metódus meghívja a ADAL-t az alkalmazással kommunikáló felhasználó hitelesítéséhez. A ADAL által biztosított **AcquireTokenAsync** metódus felszólítja a felhasználót a hitelesítő adataik megadására, és az alkalmazás a felhasználó által biztosítottak szerint folytatódik (kivéve, ha már gyorsítótárazott hitelesítő adatokkal rendelkezik):

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    var authContext = new AuthenticationContext(AuthorityUri);

    // Acquire the authentication token from Azure AD.
    var authResult = await authContext.AcquireTokenAsync(BatchResourceUri,
                                                        ClientId,
                                                        new Uri(RedirectUri),
                                                        new PlatformParameters(PromptBehavior.Auto));

    return authResult.AccessToken;
}
```

Állítson össze egy **BatchTokenCredentials** objektumot, amely a delegált paraméterként veszi át. A hitelesítő adatok használatával nyisson meg egy **BatchClient** objektumot. Ezt a **BatchClient** -objektumot használhatja a következő műveletekhez a Batch szolgáltatásban:

```csharp
public static void PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = BatchClient.Open(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        client.JobOperations.ListJobs();
    }
}
```

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-net"></a>Mintakód: Azure AD-szolgáltatásnév használata Batch .NET-tel

Ha egy egyszerű szolgáltatásnév használatával szeretne hitelesítést végezni a Batch .NET-ben, hivatkozzon a [Azure Batch .net](https://www.nuget.org/packages/Azure.Batch/) -csomagra és a [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) -csomagra.

Adja meg a következő `using` utasításokat a kódban:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Hivatkozzon a kódban található Azure AD-végpontra, beleértve a bérlő AZONOSÍTÓját. Egyszerű szolgáltatásnév használatakor meg kell adnia egy bérlői specifikus végpontot. A bérlő AZONOSÍTÓjának lekéréséhez kövesse a [Azure Active Directory bérlői azonosítójának lekérése](#get-the-tenant-id-for-your-active-directory)című szakasz lépéseit:

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

A Batch szolgáltatás erőforrás-végpontjának hivatkozása:

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

A Batch-fiókra való hivatkozás:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Adja meg az alkalmazás AZONOSÍTÓját (ügyfél-AZONOSÍTÓját). Az alkalmazás-azonosító elérhető az alkalmazás regisztrációjában a Azure Portalban:

```csharp
private const string ClientId = "<application-id>";
```

A Azure Portalból másolt titkos kulcs meghatározása:

```csharp
private const string ClientKey = "<secret-key>";
```

A hitelesítési jogkivonat Azure AD-ből való beszerzéséhez írjon be egy visszahívási módszert. Az itt látható **GetAuthenticationTokenAsync** visszahívási módszer a felügyelet nélküli hitelesítéshez ADAL hív meg:

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync(BatchResourceUri, new ClientCredential(ClientId, ClientKey));

    return authResult.AccessToken;
}
```

Állítson össze egy **BatchTokenCredentials** objektumot, amely a delegált paraméterként veszi át. A hitelesítő adatok használatával nyisson meg egy **BatchClient** objektumot. Ezután használja a **BatchClient** objektumot a következő műveletekhez a Batch szolgáltatáshoz:

```csharp
public static void PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = BatchClient.Open(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        client.JobOperations.ListJobs();
    }
}
```

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-python"></a>Mintakód: Azure AD-szolgáltatásnév használata batch Python használatával

Ha egy egyszerű szolgáltatásnevet szeretne hitelesíteni a Batch Pythonból, telepítse és hivatkozzon az [Azure-batch](https://pypi.org/project/azure-batch/) és az [Azure-Common](https://pypi.org/project/azure-common/) modulokra.

```python
from azure.batch import BatchServiceClient
from azure.common.credentials import ServicePrincipalCredentials
```

Egyszerű szolgáltatásnév használatakor meg kell adnia a bérlő AZONOSÍTÓját. A bérlő AZONOSÍTÓjának lekéréséhez kövesse a [Azure Active Directory bérlői azonosítójának lekérése](#get-the-tenant-id-for-your-active-directory)című szakasz lépéseit:

```python
TENANT_ID = "<tenant-id>"
```

A Batch szolgáltatás erőforrás-végpontjának hivatkozása:

```python
RESOURCE = "https://batch.core.windows.net/"
```

A Batch-fiókra való hivatkozás:

```python
BATCH_ACCOUNT_URL = "https://myaccount.mylocation.batch.azure.com"
```

Adja meg az alkalmazás AZONOSÍTÓját (ügyfél-AZONOSÍTÓját). Az alkalmazás-azonosító elérhető az alkalmazás regisztrációjában a Azure Portalban:

```python
CLIENT_ID = "<application-id>"
```

A Azure Portalból másolt titkos kulcs meghatározása:

```python
SECRET = "<secret-key>"
```

Hozzon létre egy **ServicePrincipalCredentials** objektumot:

```python
credentials = ServicePrincipalCredentials(
    client_id=CLIENT_ID,
    secret=SECRET,
    tenant=TENANT_ID,
    resource=RESOURCE
)
```

A **BatchServiceClient** objektum megnyitásához használja az egyszerű szolgáltatás hitelesítő adatait. Ezt követően használja ezt a **BatchServiceClient** objektumot a Batch szolgáltatással kapcsolatos későbbi műveletekhez.

```python
    batch_client = BatchServiceClient(
    credentials,
    batch_url=BATCH_ACCOUNT_URL
)
```

## <a name="next-steps"></a>Következő lépések

- Tekintse át a [Azure Active Directory dokumentációját](../active-directory/index.yml). A ADAL használatát bemutató részletes példák az [Azure Code Samples](https://azure.microsoft.com/resources/samples/?service=active-directory) Library-ben érhetők el.
- Ismerje meg a [Azure Active Directory alkalmazás-és szolgáltatásnév-objektumait](../active-directory/develop/app-objects-and-service-principals.md) , valamint azt, hogy [miként hozható létre erőforrásokhoz hozzáférő Azure ad-alkalmazás és egyszerű szolgáltatás](../active-directory/develop/howto-create-service-principal-portal.md).
- Tudnivalók a [Batch-felügyeleti megoldások Active Directory használatával történő hitelesítéséről](batch-aad-auth-management.md).
- Az Azure AD-jogkivonattal hitelesített batch-ügyfelek létrehozásával kapcsolatban lásd: [Azure batch egyéni rendszerkép üzembe helyezése Python-parancsfájlos](https://github.com/azurebigcompute/recipes/blob/master/Azure%20Batch/CustomImages/CustomImagePython.md) minta használatával.


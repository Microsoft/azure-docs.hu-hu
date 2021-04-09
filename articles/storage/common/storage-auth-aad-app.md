---
title: Jogkivonat beszerzése az Azure AD-ből az ügyfélalkalmazástól érkező kérések engedélyezéséhez
titleSuffix: Azure Storage
description: A Azure Active Directory használatával végezhet hitelesítést egy ügyfélalkalmazás alapján, beszerezzen egy OAuth 2,0-tokent, és engedélyezi a kérelmeket az Azure Blob Storage-hoz és a várólista-tárolóhoz.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/07/2020
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: baad1a0b173ae89fec9d160572224c6cb0aa615d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103574628"
---
# <a name="acquire-a-token-from-azure-ad-for-authorizing-requests-from-a-client-application"></a>Jogkivonat beszerzése az Azure AD-ből az ügyfélalkalmazástól érkező kérések engedélyezéséhez

A Azure Active Directory (Azure AD) az Azure Blob Storage vagy a üzenetsor Storage szolgáltatással való használatának egyik legfőbb előnye, hogy a hitelesítő adatait már nem kell a kódban tárolni. Ehelyett OAuth 2,0 hozzáférési tokent igényelhet a Microsoft Identity platformon. Az Azure AD az alkalmazást futtató rendszerbiztonsági tag (felhasználó, csoport vagy egyszerű szolgáltatás) hitelesítését végzi. Ha a hitelesítés sikeres, az Azure AD visszaadja a hozzáférési jogkivonatot az alkalmazásnak, és az alkalmazás a hozzáférési token használatával engedélyezheti a kérelmeket az Azure Blob Storage-ba vagy a várólista-tárolóba.

Ez a cikk bemutatja, hogyan konfigurálhatja a natív alkalmazást vagy webalkalmazást a Microsoft Identity platformmal történő hitelesítéshez a letölthető minta alkalmazás használatával. A minta alkalmazás-funkciók .NET, de más nyelvek hasonló megközelítést használnak. További információ a Microsoft Identity platformról: a [Microsoft Identity platform áttekintése](../../active-directory/develop/v2-overview.md).

A OAuth 2,0 kód engedélyezési folyamatának áttekintését lásd: [hozzáférés engedélyezése Azure Active Directory webalkalmazásokhoz a OAuth 2,0 Code Grant flow használatával](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

## <a name="about-the-sample-application"></a>Tudnivalók a minta alkalmazásról

A minta alkalmazás teljes körű élményt nyújt, amely bemutatja, hogyan konfigurálhat egy webalkalmazást az Azure AD-vel történő hitelesítéshez egy helyi fejlesztési környezetben. A minta alkalmazás megtekintéséhez és futtatásához először klónozott vagy töltse le a [githubról](https://github.com/Azure-Samples/storage-dotnet-azure-ad-msal). Ezután kövesse a cikkben ismertetett lépéseket az Azure-alkalmazások regisztrálásának konfigurálásához és az alkalmazás frissítéséhez a környezetében.

## <a name="assign-a-role-to-an-azure-ad-security-principal"></a>Szerepkör kiosztása egy Azure AD rendszerbiztonsági tag számára

Egy rendszerbiztonsági tag Azure Storage-alkalmazásból történő hitelesítéséhez először konfigurálja az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) beállításait az adott rendszerbiztonsági tag számára. Az Azure Storage olyan beépített szerepköröket határoz meg, amelyek magukban foglalják a tárolók és a várólisták engedélyeit. Ha az Azure-szerepkör egy rendszerbiztonsági tag számára van rendelve, a rendszerbiztonsági tag hozzáférést kap az adott erőforráshoz. További információkért lásd: [hozzáférési jogosultságok kezelése az Azure Blob-és üzenetsor-adatokhoz az Azure RBAC](./storage-auth-aad-rbac-portal.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Alkalmazás regisztrálása Azure AD-Bérlővel

Az Azure AD-nek a tárolási erőforrásokhoz való hozzáférés engedélyezésére való használatának első lépéseként regisztrálja az ügyfélalkalmazás egy Azure AD-Bérlővel a [Azure Portal](https://portal.azure.com). Az ügyfélalkalmazás regisztrálása során információkat nyújt az alkalmazásról az Azure AD-hez. Az Azure AD egy ügyfél-azonosítót (más néven *alkalmazás-azonosítót*) biztosít, amelyet az alkalmazás az Azure ad-vel való hozzárendeléséhez használ futásidőben. Az ügyfél-AZONOSÍTÓval kapcsolatos további tudnivalókért tekintse meg az [alkalmazás-és szolgáltatásnév objektumait Azure Active Directoryban](../../active-directory/develop/app-objects-and-service-principals.md). Az Azure Storage-alkalmazás regisztrálásához kövesse a rövid útmutató [: alkalmazás regisztrálása a Microsoft Identity platformon](../../active-directory/develop/quickstart-configure-app-access-web-apis.md)című témakörben ismertetett lépéseket. 

Az alábbi képen a webalkalmazások regisztrálásának általános beállításai láthatók. Vegye figyelembe, hogy ebben a példában az átirányítási URI úgy van beállítva, hogy `http://localhost:5000/signin-oidc` tesztelje a minta alkalmazást a fejlesztési környezetben. Ezt a beállítást később is módosíthatja a regisztrált alkalmazás **hitelesítési** beállítása alatt a Azure Portalban:

:::image type="content" source="media/storage-auth-aad-app/app-registration.png" alt-text="A Storage-alkalmazás Azure AD-vel való regisztrálását bemutató képernyőkép":::

> [!NOTE]
> Ha natív alkalmazásként regisztrálja az alkalmazást, megadhat bármely érvényes URI-t az **átirányítási URI**-hoz. Natív alkalmazások esetén ennek az értéknek nem kell valódi URL-címnek lennie. Webalkalmazások esetén az átirányítási URI azonosítónak érvényes URI-nak kell lennie, mert meghatározza azt az URL-címet, amelyhez a tokenek meg vannak határozva.

Az alkalmazás regisztrálását követően az alkalmazás-azonosító (vagy ügyfél-azonosító) a **Beállítások** területen jelenik meg:

:::image type="content" source="media/storage-auth-aad-app/app-registration-client-id.png" alt-text="Az ügyfél-azonosítót ábrázoló képernyőkép":::

Az alkalmazások Azure AD-vel való regisztrálásával kapcsolatos további információkért lásd: [alkalmazások integrálása a Azure Active Directorysal](../../active-directory/develop/quickstart-register-app.md).

### <a name="grant-your-registered-app-permissions-to-azure-storage"></a>A regisztrált alkalmazás engedélyeinek megadása az Azure Storage-ban

Ezután adja meg az alkalmazás engedélyeit az Azure Storage API-k meghívásához. Ez a lépés lehetővé teszi, hogy az alkalmazás engedélyezze az Azure Storage-ba irányuló kéréseket az Azure AD-vel.

1. A regisztrált alkalmazás **API-engedélyei** lapon válassza az **engedély hozzáadása** elemet.
1. A **Microsoft API** -k lapon válassza az **Azure Storage** lehetőséget.
1. A **kérelem API-engedélyei** ablaktáblán, hogy **milyen típusú engedélyek szükségesek az alkalmazáshoz?**, figyelje meg, hogy a rendelkezésre álló engedélyek típusa **delegált engedély**. Ez a beállítás alapértelmezés szerint be van jelölve.
1. Az **engedélyek** területen jelölje be a **user_impersonation** melletti jelölőnégyzetet, majd kattintson az **engedélyek hozzáadása** gombra.

    :::image type="content" source="media/storage-auth-aad-app/registered-app-permissions-1.png" alt-text="A Storage API engedélyeit ábrázoló képernyőkép":::

1. Ezután adja meg a rendszergazdai jóváhagyást ezekhez az engedélyekhez a **rendszergazdai jóváhagyás megadása az alapértelmezett címtárhoz** lehetőségre kattintva.

Az **API-engedélyek** ablaktábla mostantól azt mutatja, hogy a regisztrált Azure ad-alkalmazás rendelkezik hozzáféréssel a Microsoft Graph és az Azure Storage API-khoz, és ez a beleegyező engedély az alapértelmezett címtárhoz van megadva. Az Azure AD-vel való első regisztráláskor az engedélyek Microsoft Graph automatikusan megadhatók.

:::image type="content" source="media/storage-auth-aad-app/registered-app-permissions-2.png" alt-text="A regisztrált alkalmazás API-engedélyeinek megjelenítését bemutató képernyőkép":::

### <a name="create-a-client-secret"></a>Ügyfél titkos kulcsának létrehozása

Az alkalmazásnak szüksége van egy ügyfél titkos kulcsára, hogy igazolja az identitását a jogkivonat kérésekor. Az ügyfél titkos kulcsának hozzáadásához kövesse az alábbi lépéseket:

1. Navigáljon az alkalmazás regisztrálásához a Azure Portal.
1. Válassza ki a **tanúsítványok & titkok** beállítást.
1. Az **ügyfél** titkos kulcsa területen kattintson az **új ügyfél titka** lehetőségre új titkos kulcs létrehozásához.
1. Adja meg a titkos kulcs leírását, és válassza ki a kívánt lejárati időközt.
1. Az új titok értékének azonnali másolása biztonságos helyre. A teljes érték csak egyszer jelenik meg.

    ![Az ügyfél titkos kulcsát ábrázoló képernyőfelvétel](media/storage-auth-aad-app/client-secret.png)

### <a name="enable-implicit-grant-flow"></a>Implicit engedélyezési folyamat engedélyezése

Következő lépésként konfigurálja az alkalmazás implicit engedélyezési folyamatát. Kövesse az alábbi lépéseket:

1. Navigáljon az alkalmazás regisztrálásához a Azure Portal.
1. A **kezelés** szakaszban válassza ki a **hitelesítési** beállítást.
1. Az **implicit támogatás** szakaszban jelölje be a jelölőnégyzetet az azonosító tokenek engedélyezéséhez, ahogy az az alábbi képen is látható:

    :::image type="content" source="media/storage-auth-aad-app/enable-implicit-grant-flow.png" alt-text="Az implicit engedélyezési folyamat beállításainak engedélyezését bemutató képernyőkép":::

## <a name="client-libraries-for-token-acquisition"></a>Az ügyfél kódtárai a tokenek beszerzéséhez

Miután regisztrálta az alkalmazást, és engedélyt kapott az Azure Blob Storage-ban vagy a várólista-tárolóban lévő adathozzáférésre, hozzáadhat programkódot az alkalmazáshoz egy rendszerbiztonsági tag hitelesítéséhez és egy OAuth 2,0 token beszerzéséhez. A jogkivonat hitelesítéséhez és beszerzéséhez használhatja a [Microsoft Identity platform hitelesítési kódtárainak](../../active-directory/develop/reference-v2-libraries.md) egyikét, vagy egy másik, az OpenID Connect 1,0-et támogató nyílt forráskódú könyvtárat. Az alkalmazás ezután a hozzáférési token használatával engedélyezheti a kérést az Azure Blob Storage-ban vagy a várólista-tárolóban.

A jogkivonatok beszerzését támogató forgatókönyvek listáját a [Microsoft Authentication Library (MSAL)](../../active-directory/develop/msal-overview.md) dokumentációjának [hitelesítési folyamatok](../../active-directory/develop/msal-authentication-flows.md) szakasza tartalmazza.

## <a name="well-known-values-for-authentication-with-azure-ad"></a>Az Azure AD-vel való hitelesítés jól ismert értékei

Egy rendszerbiztonsági tag Azure AD-vel történő hitelesítéséhez meg kell adnia néhány jól ismert értéket a kódban.

### <a name="azure-ad-authority"></a>Azure AD-szolgáltató

A Microsoft nyilvános felhőben az alapszintű Azure AD-szolgáltató a következő, ahol a *bérlő azonosítója* a Active Directory bérlői azonosítója (vagy a címtár azonosítója):

`https://login.microsoftonline.com/<tenant-id>/`

A bérlő azonosítója azonosítja a hitelesítéshez használandó Azure AD-bérlőt. Más néven a címtár-azonosító. A bérlő AZONOSÍTÓjának lekéréséhez navigáljon a Azure Portalban az alkalmazás regisztrációjának **Áttekintés** lapjára, és onnan másolja az értéket.

### <a name="azure-storage-resource-id"></a>Azure Storage-erőforrás azonosítója

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>.NET-kód – példa: Blokkos blob létrehozása

A Code példa bemutatja, hogyan kérhető le hozzáférési token az Azure AD-ből. A hozzáférési jogkivonat a megadott felhasználó hitelesítésére szolgál, majd egy blokk-blob létrehozására vonatkozó kérést engedélyez. A minta működésének megkezdéséhez kövesse az előző szakaszokban ismertetett lépéseket.

A jogkivonat igényléséhez az alkalmazás regisztrációja után a következő értékekre lesz szüksége:

- Az Azure AD-tartomány neve. Ezt az értéket a Azure Active Directory **Áttekintés** lapjáról kérheti le.
- A bérlő (vagy könyvtár) azonosítója. Ezt az értéket az alkalmazás regisztrációjának **Áttekintés** lapjáról kérheti le.
- Az ügyfél (vagy alkalmazás) azonosítója. Ezt az értéket az alkalmazás regisztrációjának **Áttekintés** lapjáról kérheti le.
- Az ügyfél átirányítási URI-ja. Az alkalmazás regisztrációjának **hitelesítési** beállításaiból olvassa be ezt az értéket.
- Az ügyfél titkos kulcsának értéke. Az érték lekérése abból a helyről, amelyre korábban már másolta.

### <a name="create-a-storage-account-and-container"></a>Storage-fiók és-tároló létrehozása

A kód futtatásához hozzon létre egy Storage-fiókot a Azure Active Directoryével megegyező előfizetésen belül. Ezután hozzon létre egy tárolót a Storage-fiókon belül. A mintakód egy blokk-blobot hoz létre ebben a tárolóban.

Ezután explicit módon rendelje hozzá a **Storage blob-adatközreműködői** szerepkört ahhoz a felhasználói fiókhoz, amelyen a kódot futtatni fogja. A szerepkörnek a Azure Portal való hozzárendelésével kapcsolatos útmutatásért lásd: [a Azure Portal használata Azure-szerepkör hozzárendeléséhez a blob-és üzenetsor-információk eléréséhez](storage-auth-aad-rbac-portal.md).

> [!NOTE]
> Azure Storage-fiók létrehozásakor nem rendel automatikusan engedélyeket az Azure AD-n keresztüli adateléréshez. Explicit módon hozzá kell rendelnie egy Azure-szerepkört az Azure Storage-hoz. Az előfizetés, az erőforráscsoport, a Storage-fiók, a tároló vagy a várólista szintjén rendelhető hozzá.
>
> Az adathozzáféréshez szükséges szerepkör kiosztása előtt a Storage-fiókban lévő adataihoz a Azure Portal keresztül férhet hozzá, mivel a Azure Portal az adathozzáféréshez is használhatja a fiók kulcsát. További információ: [a blob-adatokhoz való hozzáférés engedélyezésének kiválasztása a Azure Portalban](../blobs/authorize-data-operations-portal.md).

### <a name="create-a-web-application-that-authorizes-access-to-blob-storage-with-azure-ad"></a>Webalkalmazás létrehozása, amely engedélyezi a blob Storage-hoz való hozzáférést az Azure AD-vel

Ha az alkalmazás hozzáfér az Azure Storage-hoz, azt a felhasználó nevében teszi meg, ami azt jelenti, hogy a blob vagy a várólista erőforrásai hozzáférnek a bejelentkezett felhasználó engedélyeivel. A kód kipróbálásához például egy webalkalmazásra van szükség, amely felszólítja a felhasználót, hogy jelentkezzen be egy Azure AD-identitás használatával. Létrehozhat saját, vagy használhatja a Microsoft által biztosított minta alkalmazást.

Egy befejezett minta webalkalmazás, amely tokent vásárol, és az Azure Storage-ban lévő blob létrehozásához használja a [githubon](https://aka.ms/aadstorage). A befejezett minta áttekintése és futtatása hasznos lehet a példákban szereplő példák megismerése érdekében. A befejezett minta futtatásával kapcsolatos utasításokért tekintse meg a következő témakört: [nézet és a befejezett minta futtatása](#view-and-run-the-completed-sample).

#### <a name="add-references-and-using-statements"></a>Hivatkozások hozzáadása és utasítások használata  

Telepítse az Azure Storage ügyféloldali kódtárat a Visual studióból. A **Tools** (Eszközök) menüben válassza a **NuGet Package Manager** (NuGet-csomagkezelő), majd a **Package Manager Console** (Csomagkezelői konzol) lehetőséget. Írja be a következő parancsokat a konzol ablakába a szükséges csomagok telepítéséhez a .NET-hez készült Azure Storage ügyféloldali kódtáraból:

# <a name="net-v12-sdk"></a>[.NET V12 SDK](#tab/dotnet)

```console
Install-Package Azure.Storage.Blobs
Install-Package Microsoft.Identity.Web -Version 0.4.0-preview
```

Ezután adja hozzá a következő using utasításokat a HomeController. cs fájlhoz:

```csharp
using Microsoft.Identity.Web; //MSAL library for getting the access token
using Azure.Storage.Blobs;
```

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Identity.Web -Version 0.4.0-preview //or a later version
```

Ezután adja hozzá a következő using utasításokat a HomeController. cs fájlhoz:

```csharp
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
```

---

#### <a name="create-a-block-blob"></a>Blokkos blob létrehozása

Adja hozzá a következő kódrészletet egy blokk blob létrehozásához. Ne felejtse el lecserélni a szögletes zárójelben lévő értékeket a saját értékeire:

# <a name="net-v12-sdk"></a>[.NET V12 SDK](#tab/dotnet)

```csharp
private static async Task<string> CreateBlob(TokenAcquisitionTokenCredential tokenCredential)
{
    Uri blobUri = new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt");
    BlobClient blobClient = new BlobClient(blobUri, tokenCredential);

    string blobContents = "Blob created by Azure AD authenticated user.";
    byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

    using (MemoryStream stream = new MemoryStream(byteArray))
    {
        await blobClient.UploadAsync(stream);
    }
    return "Blob successfully created";
}
```

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

```csharp
private static async Task<string> CreateBlob(string accessToken)
{
    // Create a blob on behalf of the user.
    TokenCredential tokenCredential = new TokenCredential(accessToken);
    StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

    // Replace the URL below with the URL to your blob.
    Uri blobUri = new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt");
    CloudBlockBlob blob = new CloudBlockBlob(blobUri, storageCredentials);
    await blob.UploadTextAsync("Blob created by Azure AD authenticated user.");
    return "Blob successfully created";
}
```

---

> [!NOTE]
> A blob-és üzenetsor-műveletek OAuth 2,0-tokenrel való engedélyezéséhez a HTTPS protokollt kell használnia.

A fenti példában a .NET-ügyfél könyvtára kezeli a kérelem engedélyezését a blokk blob létrehozásához. Az Azure Storage ügyféloldali kódtárai más nyelveken is kezelik a kérések engedélyezését. Ha azonban egy Azure Storage-műveletet hív meg egy OAuth-tokenrel a REST API használatával, akkor az **engedélyezési** fejlécet az OAuth-token használatával kell létrehoznia.

A blobok és Queue szolgáltatás műveletek OAuth hozzáférési jogkivonatokkal történő meghívásához adja át a hozzáférési jogkivonatot az **engedélyezési** fejlécben a **tulajdonosi** séma használatával, és adjon meg egy 2017-11-09-es vagy magasabb verziójú Service-verziót az alábbi példában látható módon:

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

#### <a name="get-an-access-token-from-azure-ad"></a>Hozzáférési token lekérése a Microsoft Azure Active Directory-ból

Ezután adjon hozzá egy metódust, amely jogkivonatot kér az Azure AD-től a felhasználó nevében. Ez a metódus határozza meg azt a hatókört, amelyhez engedélyeket kell megadni. Az engedélyekkel és hatókörökkel kapcsolatos további információkért tekintse [meg a Microsoft Identity platform végpontjának engedélyek és](../../active-directory/develop/v2-permissions-and-consent.md)hozzájárulások című témakörét.

Az erőforrás-AZONOSÍTÓval hozza létre azt a hatókört, amelyhez a jogkivonat beszerzését kéri. A példa a hatókört az erőforrás-azonosító és a beépített hatókör használatával hozza létre `user_impersonation` , amely azt jelzi, hogy a jogkivonatot a felhasználó nevében kérték.

Ne feledje, hogy előfordulhat, hogy a felhasználót olyan felülettel kell bemutatnia, amely lehetővé teszi, hogy a felhasználó beleegyezik a jogkivonat kérelmére:

```csharp
[AuthorizeForScopes(Scopes = new string[] { "https://storage.azure.com/user_impersonation" })]
public async Task<IActionResult> Blob()
{
    string message = await CreateBlob(new TokenAcquisitionTokenCredential(_tokenAcquisition));
    ViewData["Message"] = message;
    return View();
}
```

A hozzájárulás egy olyan felhasználó, aki engedélyt ad egy alkalmazásnak a védett erőforrások elérésére a nevükben. A Microsoft Identity platform támogatja a növekményes hozzáférést, ami azt jelenti, hogy egy rendszerbiztonsági tag először kérheti le az engedélyek minimális készletét, és igény szerint adja hozzá az engedélyeket. Ha a kód hozzáférési jogkivonatot kér, adja meg az alkalmazás által igényelt engedélyek hatókörét. További információ a növekményes megállapodásról: [növekményes és dinamikus beleegyezett](../../active-directory/azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent).

## <a name="view-and-run-the-completed-sample"></a>A befejezett minta megtekintése és futtatása

A minta alkalmazás futtatásához először klónozott vagy töltse le a [githubról](https://github.com/Azure-Samples/storage-dotnet-azure-ad-msal). Ezután frissítse az alkalmazást a következő szakaszokban leírtak szerint.

### <a name="provide-values-in-the-settings-file"></a>Értékek megadása a beállítási fájlban

Frissítse a fájl *appsettings.jsa* saját értékeivel, a következőképpen:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "<azure-ad-domain-name>.onmicrosoft.com",
    "TenantId": "<tenant-id>",
    "ClientId": "<client-id>",
    "ClientSecret": "<client-secret>",
    "ClientCertificates": [
    ],
    "CallbackPath": "/signin-oidc"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="update-the-storage-account-and-container-name"></a>A Storage-fiók és a tároló nevének frissítése

A *HomeController. cs* fájlban frissítse a blokk blobra hivatkozó URI-t a Storage-fiók és a tároló nevének használatához, és az értékeket a saját értékeivel cserélje le a szögletes zárójelben:

```html
https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt
```

## <a name="next-steps"></a>Következő lépések

- [Microsoft-identitásplatform](../../active-directory/develop/index.yml)
- [A tárolási és a hozzáférési jogosultságok kezelése az Azure RBAC](./storage-auth-aad-rbac-portal.md)
- [Blobok és várólisták hozzáférésének hitelesítése Azure Active Directory és felügyelt identitásokkal az Azure-erőforrásokhoz](storage-auth-aad-msi.md)

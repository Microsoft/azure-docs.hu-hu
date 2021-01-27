---
title: Hozzáférés engedélyezése felügyelt identitással rendelkező adatokhoz
titleSuffix: Azure Storage
description: Felügyelt identitások használata az Azure-erőforrásokhoz a blob-és üzenetsor-hozzáférés engedélyezéséhez az Azure-beli virtuális gépeken, a Function Appsben és más szolgáltatásokban futó alkalmazásokból.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/07/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 552d2587f35ed391b470c6d5b1693b79fd57306b
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879578"
---
# <a name="authorize-access-to-blob-and-queue-data-with-managed-identities-for-azure-resources"></a>Hozzáférés engedélyezése a blob-és üzenetsor-szolgáltatásokhoz az Azure-erőforrások felügyelt identitásával

Az Azure Blob és a üzenetsor-tároló támogatja a Azure Active Directory (Azure AD) hitelesítést az [Azure-erőforrások felügyelt identitásával](../../active-directory/managed-identities-azure-resources/overview.md). Az Azure-erőforrások felügyelt identitásai engedélyezhetik a blob-és üzenetsor-adatokhoz való hozzáférést az Azure-beli virtuális gépeken (VM-ben) futó alkalmazások Azure AD-beli hitelesítő adataival, a Function apps, a virtuálisgép-méretezési csoportokkal és más szolgáltatásokkal. Ha felügyelt identitásokat használ az Azure-erőforrásokhoz az Azure AD-hitelesítéssel együtt, elkerülheti a hitelesítő adatok tárolását a felhőben futó alkalmazásaival.  

Ez a cikk bemutatja, hogyan engedélyezhető a blob-vagy üzenetsor-adatokhoz való hozzáférés az Azure-beli virtuális gépekről az Azure-erőforrások felügyelt identitásai használatával. Azt is leírja, hogyan tesztelheti a kódot a fejlesztői környezetben.

## <a name="enable-managed-identities-on-a-vm"></a>Felügyelt identitások engedélyezése egy virtuális gépen

Ahhoz, hogy az Azure-erőforrásokhoz felügyelt identitások használatával engedélyezzék a blobokhoz és a várólistákhoz való hozzáférést a virtuális gépről, először engedélyeznie kell a felügyelt identitásokat az Azure-erőforrásokhoz a virtuális gépen. Ha szeretné megtudni, hogyan engedélyezheti az Azure-erőforrások felügyelt identitásait, tekintse meg az alábbi cikkek egyikét:

- [Azure Portalra](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sablon](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager ügyféloldali kódtárak](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

A felügyelt identitásokkal kapcsolatos további információkért lásd: [felügyelt identitások az Azure-erőforrásokhoz](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="authenticate-with-the-azure-identity-library"></a>Hitelesítés az Azure Identity Library használatával

Az Azure Identity ügyféloldali kódtára az Azure AD-jogkivonat hitelesítésének támogatását biztosítja az Azure [SDK](https://github.com/Azure/azure-sdk)-hoz. Az Azure Storage ügyféloldali kódtárainak legújabb verziói a .NET, a Java, a Python és a JavaScript integrálására az Azure Identity Library használatával egyszerű és biztonságos eszközöket biztosítanak az Azure Storage-kérelmek engedélyezéséhez szükséges OAuth 2,0-token beszerzéséhez.

Az Azure Identity Client Library előnye, hogy lehetővé teszi, hogy ugyanazt a kódot használja annak hitelesítésére, hogy az alkalmazás a fejlesztői környezetben vagy az Azure-ban fut-e. Az Azure Identity .NET-hez készült ügyféloldali kódtára hitelesíti a rendszerbiztonsági tag nevét. Ha a kód az Azure-ban fut, a rendszerbiztonsági tag felügyelt identitás az Azure-erőforrásokhoz. A fejlesztői környezetben a felügyelt identitás nem létezik, így az ügyfél-függvénytár tesztelési célból hitelesíti a felhasználót vagy az egyszerű szolgáltatást.

A hitelesítés után az Azure Identity Client Library megkapja a jogkivonat hitelesítő adatait. Ezt a jogkivonat hitelesítő adatait a rendszer az Azure Storage szolgáltatással kapcsolatos műveletek elvégzéséhez létrehozott szolgáltatási ügyfél objektumba ágyazza be. A könyvtár a megfelelő jogkivonat-hitelesítő adatok lekérésével zökkenőmentesen kezeli ezt.

A .NET-hez készült Azure Identity ügyféloldali kódtáraval kapcsolatos további információkért lásd: az [Azure Identity ügyféloldali kódtára a .net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)-hez. Az Azure Identity ügyféloldali kódtár dokumentációját lásd: az [Azure. Identity névtér](/dotnet/api/azure.identity).

### <a name="assign-azure-roles-for-access-to-data"></a>Azure-szerepkörök kiosztása az adathoz való hozzáféréshez

Ha egy Azure AD rendszerbiztonsági tag megpróbál hozzáférni a blob-vagy üzenetsor-adatszolgáltatáshoz, akkor a rendszerbiztonsági tag engedélyekkel kell rendelkeznie az erőforráshoz. Azt jelzi, hogy a rendszerbiztonsági tag felügyelt identitás-e az Azure-ban vagy egy olyan Azure AD-felhasználói fiókban, amely kódot futtat a fejlesztési környezetben, a rendszerbiztonsági tag számára olyan Azure-szerepkört kell hozzárendelni, amely hozzáférést biztosít az Azure Storage-beli blob vagy üzenetsor Az engedélyek Azure RBAC való hozzárendelésével kapcsolatos információkért tekintse meg az Azure [-blobok és-várólisták hozzáférésének engedélyezése az Azure-beli blobokhoz és-várólistákhoz Azure Active Directory használatával](../common/storage-auth-aad.md#assign-azure-roles-for-access-rights)című szakaszt. 

> [!NOTE]
> Azure Storage-fiók létrehozásakor nem rendel automatikusan engedélyeket az Azure AD-n keresztüli adateléréshez. Explicit módon hozzá kell rendelnie egy Azure-szerepkört az Azure Storage-hoz. Az előfizetés, az erőforráscsoport, a Storage-fiók, a tároló vagy a várólista szintjén rendelhető hozzá.
>
> Az adathozzáféréshez szükséges szerepkör kiosztása előtt a Storage-fiókban lévő adataihoz a Azure Portal keresztül férhet hozzá, mivel a Azure Portal az adathozzáféréshez is használhatja a fiók kulcsát. További információ: [a blob-adatokhoz való hozzáférés engedélyezésének kiválasztása a Azure Portalban](../blobs/authorize-data-operations-portal.md).

### <a name="authenticate-the-user-in-the-development-environment"></a>A felhasználó hitelesítése a fejlesztői környezetben

Ha a kód a fejlesztési környezetben fut, a hitelesítés automatikusan kezelhető, vagy szükség lehet egy böngészőbeli bejelentkezésre, attól függően, hogy melyik eszközt használja. A Microsoft Visual Studio például támogatja az egyszeri bejelentkezést (SSO), így az aktív Azure AD-felhasználói fiók automatikusan használatos a hitelesítéshez. További információ az egyszeri bejelentkezésről: [egyszeri bejelentkezés az alkalmazásokba](../../active-directory/manage-apps/what-is-single-sign-on.md).

Más fejlesztői eszközök megkérhetik a bejelentkezést egy webböngészőn keresztül.

### <a name="authenticate-a-service-principal-in-the-development-environment"></a>Egyszerű szolgáltatásnév hitelesítése a fejlesztői környezetben

Ha a fejlesztési környezet nem támogatja az egyszeri bejelentkezést vagy a bejelentkezést egy webböngészőn keresztül, akkor egy egyszerű szolgáltatásnév használatával végezheti el a hitelesítést a fejlesztői környezetből.

#### <a name="create-the-service-principal"></a>A szolgáltatásnév létrehozása

Az Azure CLI-vel és az Azure-szerepkörök hozzárendelésével kapcsolatos egyszerű szolgáltatás létrehozásához hívja az az [ad SP Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) parancsot. Adjon meg egy Azure Storage-adathozzáférési szerepkört az új egyszerű szolgáltatáshoz való hozzárendeléshez. Továbbá adja meg a szerepkör-hozzárendelés hatókörét. Az Azure Storage beépített szerepköreivel kapcsolatos további információkért lásd: az [Azure beépített szerepkörei](../../role-based-access-control/built-in-roles.md).

Ha nem rendelkezik megfelelő engedélyekkel ahhoz, hogy szerepkört rendeljen a szolgáltatáshoz, előfordulhat, hogy meg kell kérnie a fiók tulajdonosát vagy a rendszergazdát, hogy elvégezze a szerepkör-hozzárendelést.

Az alábbi példa az Azure CLI-t használja egy új egyszerű szolgáltatásnév létrehozásához, és hozzárendeli a **Storage blob Adatolvasói** szerepkört a fiók hatóköréhez

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Contributor" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

A `az ad sp create-for-rbac` parancs JSON formátumban adja vissza a szolgáltatásnév tulajdonságainak listáját. Másolja ezeket az értékeket, hogy felhasználhassa a szükséges környezeti változókat a következő lépésben.

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```

> [!IMPORTANT]
> Az Azure-beli szerepkör-hozzárendelések eltartása néhány percet is igénybe vehet.

#### <a name="set-environment-variables"></a>Környezeti változók beállítása

Az Azure Identity ügyféloldali függvénytár három környezeti változóból olvassa be az értékeket az egyszerű szolgáltatásnév hitelesítéséhez. A következő táblázat az egyes környezeti változókhoz beállított értéket ismerteti.

|Környezeti változó|Érték
|-|-
|`AZURE_CLIENT_ID`|Az egyszerű szolgáltatáshoz tartozó alkalmazás azonosítója
|`AZURE_TENANT_ID`|Az egyszerű szolgáltatás Azure AD-bérlői azonosítója
|`AZURE_CLIENT_SECRET`|Az egyszerű szolgáltatásnév számára létrehozott jelszó

> [!IMPORTANT]
> A környezeti változók beállítása után zárjuk be és nyissa meg újra a konzolablak ablakát. Ha a Visual studiót vagy más fejlesztési környezetet használ, előfordulhat, hogy újra kell indítania a fejlesztési környezetet ahhoz, hogy regisztrálni lehessen az új környezeti változókat.

További információkért lásd: [identitás létrehozása az Azure-alkalmazáshoz a portálon](../../active-directory/develop/howto-create-service-principal-portal.md).

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>.NET-kód – példa: Blokkos blob létrehozása

Adja hozzá a következő `using` irányelveket a kódhoz az Azure Identity és az Azure Storage ügyféloldali kódtárainak használatához.

```csharp
using Azure;
using Azure.Identity;
using Azure.Storage.Blobs;
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
```

Ha meg szeretné kapni a jogkivonat hitelesítő adatait, amelyet a kód használhat az Azure Storage-ba irányuló kérések engedélyezéséhez, hozza létre a [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) osztály egy példányát. A következő mintakód bemutatja, hogyan kérheti le a hitelesített jogkivonat hitelesítő adatait, és hogyan használhatja szolgáltatás-ügyfél objektum létrehozására, majd a szolgáltatás-ügyféllel feltöltheti az új blobokat:

```csharp
async static Task CreateBlockBlobAsync(string accountName, string containerName, string blobName)
{
    // Construct the blob container endpoint from the arguments.
    string containerEndpoint = string.Format("https://{0}.blob.core.windows.net/{1}",
                                                accountName,
                                                containerName);

    // Get a credential and create a client object for the blob container.
    BlobContainerClient containerClient = new BlobContainerClient(new Uri(containerEndpoint),
                                                                    new DefaultAzureCredential());

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload text to a new block blob.
        string blobContents = "This is a block blob.";
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            await containerClient.UploadBlobAsync(blobName, stream);
        }
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

> [!NOTE]
> Az Azure AD-vel a blob-vagy üzenetsor-adatkérések engedélyezéséhez HTTPS protokollt kell használnia a kérelmekhez.

## <a name="next-steps"></a>További lépések

- [A tárolási és az Azure RBAC hozzáférési jogosultságának kezelése](./storage-auth-aad-rbac-portal.md).
- [Használja az Azure ad-t a Storage-alkalmazásokhoz](storage-auth-aad-app.md).
- [PowerShell-parancsok futtatása Azure AD-beli hitelesítő adatokkal a Blobok adatainak eléréséhez](../blobs/authorize-data-operations-powershell.md)
- [Oktatóanyag: hozzáférés a Storage-ból App Service felügyelt identitások használatával](../../app-service/scenario-secure-app-access-storage.md)
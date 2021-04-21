---
title: Adatok hozzáférésének jogosultsága felügyelt identitással
titleSuffix: Azure Storage
description: Az Azure-erőforrások felügyelt identitásaival engedélyezheti a blob- és üzenetsoradatok hozzáférését az Azure-beli virtuális gépeken, függvényalkalmazásokban stb. futó alkalmazásokból.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/07/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 80df7b85ec1ad9e273081f9a6a96b9a9d7ec8cd9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791192"
---
# <a name="authorize-access-to-blob-and-queue-data-with-managed-identities-for-azure-resources"></a>Blob- és üzenetsoradatokhoz való hozzáférés jogosultsága az Azure-erőforrások felügyelt identitásával

Az Azure Blob and Queue Storage támogatja Azure Active Directory Azure AD-hitelesítést az [Azure-erőforrások felügyelt identitásával.](../../active-directory/managed-identities-azure-resources/overview.md) Az Azure-erőforrások felügyelt identitásai Azure AD-beli hitelesítő adatokkal engedélyezhetik a blob- és üzenetsoradatokhoz való hozzáférést az Azure-beli virtuális gépeken (VM-ekben), függvényalkalmazásokban, virtuálisgép-méretezési készletekben és más szolgáltatásokban futó alkalmazásokból. Az Azure-erőforrások felügyelt identitásai és az Azure AD-hitelesítés együttes használatával elkerülheti a hitelesítő adatok tárolását a felhőben futó alkalmazásokban.  

Ez a cikk bemutatja, hogyan engedélyezheti a blob- vagy üzenetsoradatokhoz való hozzáférést egy Azure-beli virtuális gépről az Azure-erőforrások felügyelt identitásait használva. Azt is bemutatja, hogyan tesztelje a kódot a fejlesztési környezetben.

## <a name="enable-managed-identities-on-a-vm"></a>Felügyelt identitások engedélyezése virtuális gépen

Ahhoz, hogy az Azure-erőforrások felügyelt identitásai használatával engedélyezze a blobokhoz és üzenetsorokhoz való hozzáférést a virtuális gépről, először engedélyeznie kell az Azure-erőforrások felügyelt identitását a virtuális gépen. Az Azure-erőforrások felügyelt identitásának engedélyezéséről az alábbi cikkekben talál további információt:

- [Azure Portalra](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sablon](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager-kódtárak létrehozása](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

További információ a felügyelt identitásokkal kapcsolatban: [Felügyelt identitások Azure-erőforrásokhoz.](../../active-directory/managed-identities-azure-resources/overview.md)

## <a name="authenticate-with-the-azure-identity-library"></a>Hitelesítés az Azure Identity Libraryvel

Az Azure Identity ügyféloldali kódtára Azure AD-jogkivonat-hitelesítést biztosít az [Azure SDK-hoz.](https://github.com/Azure/azure-sdk) Az Azure Storage .NET-hez, Javához, Pythonhoz és JavaScripthez készült ügyfélkódtárainak legújabb verziói integrálhatók az Azure Identity-kódtárakba, így egyszerű és biztonságos módon szerezhető be OAuth 2.0-jogkivonat az Azure Storage-kérelmek hitelesítésére.

Az Azure Identity ügyféloldali kódtár előnye, hogy lehetővé teszi, hogy ugyanazt a kódot használja annak hitelesítéséhez, hogy az alkalmazás a fejlesztési környezetben vagy az Azure-ban fut-e. Az Azure Identity .NET-hez való ügyféloldali kódtára hitelesít egy rendszerbiztonsági biztonsági szolgáltatásokat. Ha a kód az Azure-ban fut, a rendszerbiztonsági tag az Azure-erőforrások felügyelt identitása. A fejlesztési környezetben a felügyelt identitás nem létezik, ezért az ügyféloldali kódtár a felhasználót vagy egy egyszerű szolgáltatást hitelesít tesztelési célból.

A hitelesítés után az Azure Identity ügyféloldali kódtára megkapja a jogkivonat hitelesítő adatait. Ezt a jogkivonat-hitelesítő adatokat a rendszer ezután beágyazja a szolgáltatás ügyfélobjektumba, amelyből műveleteket hajthat végre az Azure Storage-ban. A kódtár ezt zökkenőmentesen kezeli a megfelelő jogkivonat-hitelesítő adatok lekért használatával.

Az Azure Identity .NET-hez való ügyféloldali kódtárával kapcsolatos további információkért lásd: [Az Azure Identity .NET-hez](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)való ügyféloldali kódtára. Az Azure Identity ügyféloldali kódtár referenciadokumentációja: [Azure.Identity Namespace](/dotnet/api/azure.identity).

### <a name="assign-azure-roles-for-access-to-data"></a>Azure-szerepkörök hozzárendelése az adatokhoz való hozzáféréshez

Amikor egy Azure AD-beli rendszerbiztonsági tag megpróbál hozzáférni a blob- vagy üzenetsoradatokhoz, a rendszerbiztonsági tagnak engedéllyel kell rendelkeznie az erőforráshoz. Függetlenül attól, hogy a rendszerbiztonsági tag felügyelt identitás az Azure-ban, vagy a fejlesztési környezetben kódot futtató Azure AD felhasználói fiók, a rendszerbiztonsági taghoz olyan Azure-szerepkört kell rendelni, amely hozzáférést biztosít az Azure Storage blob- vagy üzenetsor-adataihoz. Az engedélyek Azure RBAC-n keresztül történő hozzárendelésével  kapcsolatos információkért lásd az Azure-blobok és -üzenetsorok elérésének az Azure-blobok és -üzenetsorok elérésének az azure-beli blobokkal és üzenetsorokkal való hozzáférésének [Azure Active Directory.](../common/storage-auth-aad.md#assign-azure-roles-for-access-rights)

> [!NOTE]
> Azure Storage-fiók létrehozásakor nem kap automatikusan engedélyeket az adatok Azure AD-n keresztüli eléréséhez. Explicit módon ki kell rendelnie magának egy Azure-szerepkört az Azure Storage-hoz. Hozzárendelheti az előfizetése, erőforráscsoportja, tárfiókja, tárolója vagy üzenetsora szintjén.
>
> Mielőtt szerepkört rendel saját magának az adateléréshez, hozzáférhet a tárfiókban az Azure Portal-n keresztül, mert a Azure Portal a fiókkulcsot is használhatja az adateléréshez. További információ: [Choose how to authorize access to blob data in the Azure Portal.](../blobs/authorize-data-operations-portal.md)

### <a name="authenticate-the-user-in-the-development-environment"></a>A felhasználó hitelesítése a fejlesztési környezetben

Ha a kód a fejlesztési környezetben fut, a hitelesítés kezelése automatikusan is megtörténik, vagy a használt eszközöktől függően böngészőbe való bejelentkezésre lehet szükség. A Microsoft Visual Studio támogatja az egyszeri bejelentkezést (SSO), így a rendszer automatikusan az aktív Azure AD-felhasználói fiókot használja a hitelesítéshez. Az SSO-val kapcsolatos további információkért lásd: Egyszeri bejelentkezés [az alkalmazásokba.](../../active-directory/manage-apps/what-is-single-sign-on.md)

Előfordulhat, hogy más fejlesztői eszközök arra kérik, hogy egy webböngészőn keresztül jelentkezzen be.

### <a name="authenticate-a-service-principal-in-the-development-environment"></a>Szolgáltatásnév hitelesítése a fejlesztési környezetben

Ha a fejlesztési környezet nem támogatja az egyszeri bejelentkezést vagy a webböngészőn keresztüli bejelentkezést, akkor szolgáltatásnév használatával hitelesíthet a fejlesztési környezetből.

#### <a name="create-the-service-principal"></a>A szolgáltatásnév létrehozása

Ha szolgáltatásnévt szeretne létrehozni az Azure CLI használatával, és azure-szerepkört szeretne hozzárendelni, hívja meg az [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) parancsot. Adjon meg egy Azure Storage adatelérési szerepkört az új szolgáltatásnévhez való hozzárendeléshez. Emellett adja meg a szerepkör-hozzárendelés hatókörét. További információ az Azure Storage beépített szerepköreiről: [Az Azure beépített szerepkörei.](../../role-based-access-control/built-in-roles.md)

Ha nem rendelkezik megfelelő engedélyekkel ahhoz, hogy szerepkört rendeljen a szolgáltatásnévhez, előfordulhat, hogy meg kell kérnie a fióktulajdonost vagy a rendszergazdát a szerepkör-hozzárendelés elvégzésére.

Az alábbi példa az Azure CLI-t használja egy új szolgáltatásnév létrehozásához, és a **Storage-blobadatok** olvasó szerepkörének hozzárendeléséhez fiókhatókörrel

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Contributor" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

A `az ad sp create-for-rbac` parancs visszaadja a szolgáltatásnév tulajdonságainak listáját JSON formátumban. Másolja ki ezeket az értékeket, hogy a következő lépésben létre tudja hozni a szükséges környezeti változókat.

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
> Az Azure-beli szerepkör-hozzárendelések propagálása eltarthat néhány percig.

#### <a name="set-environment-variables"></a>Környezeti változók beállítása

Az Azure Identity ügyféloldali kódtára futtatáskor három környezeti változó értékeit olvassa be a szolgáltatásnév hitelesítéséhez. Az alábbi táblázat ismerteti az egyes környezeti változókhoz beállítható értéket.

|Környezeti változó|Érték
|-|-
|`AZURE_CLIENT_ID`|A szolgáltatásnév alkalmazásazonosítója
|`AZURE_TENANT_ID`|A szolgáltatásnév Azure AD-bérlőazonosítója
|`AZURE_CLIENT_SECRET`|A szolgáltatásnévhez létrehozott jelszó

> [!IMPORTANT]
> A környezeti változók beállítása után zárja be, majd nyissa meg újra a konzolablakot. Ha új vagy Visual Studio környezetet használ, előfordulhat, hogy újra kell indítania a fejlesztési környezetet ahhoz, hogy regisztrálja az új környezeti változókat.

További információ: Identitás létrehozása [Azure-alkalmazáshoz a portálon.](../../active-directory/develop/howto-create-service-principal-portal.md)

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>.NET-példakód: Blokkblob létrehozása

Adja hozzá a következő `using` irányelveket a kódhoz az Azure Identity és az Azure Storage ügyfélkódtárainak használatával.

```csharp
using Azure;
using Azure.Identity;
using Azure.Storage.Blobs;
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
```

Egy jogkivonat hitelesítő adatainak lekérése, amely alapján a kód engedélyezheti az Azure Storage-hoz való kéréseket, hozza létre a [DefaultAzureCredential osztály egy példányát.](/dotnet/api/azure.identity.defaultazurecredential) Az alábbi példakód bemutatja, hogyan kérhető le a hitelesített jogkivonat hitelesítő adatai, és hogyan használható egy szolgáltatás ügyfélobjektumának létrehozására, majd hogyan használható a szolgáltatás ügyfélprogramja egy új blob feltöltésére:

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
> Blob- vagy üzenetsoradatokra vonatkozó kérések Azure AD-val való hitelesítéséhez HTTPS-t kell használnia ezekhez a kérésekhez.

## <a name="next-steps"></a>Következő lépések

- [A tárolási adatok hozzáférési jogosultságának kezelése az Azure RBAC használatával.](./storage-auth-aad-rbac-portal.md)
- [Az Azure AD használata tárolóalkalmazásokkal.](storage-auth-aad-app.md)
- [PowerShell-parancsok futtatása Azure AD hitelesítő adatokkal a blobadatok eléréséhez](../blobs/authorize-data-operations-powershell.md)
- [Oktatóanyag: Tárterület elérése App Service felügyelt identitások használatával](../../app-service/scenario-secure-app-access-storage.md)

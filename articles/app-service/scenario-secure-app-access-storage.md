---
title: Oktatóanyag – A webalkalmazás felügyelt identitásokkal fér hozzá a | Azure
description: Ez az oktatóanyag bemutatja, hogyan férhet hozzá az Azure Storage-hoz egy alkalmazáshoz felügyelt identitások használatával.
services: storage, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/30/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1, devx-track-azurecli
ms.openlocfilehash: 7d84b3f8e654940a8f2c36075b92d630505e88b9
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482317"
---
# <a name="tutorial-access-azure-storage-from-a-web-app"></a>Oktatóanyag: Az Azure Storage elérése webalkalmazásból

Megtudhatja, hogyan férhet hozzá az Azure Storage-hoz egy olyan webalkalmazáshoz (nem egy bejelentkezett felhasználóhoz), amely a Azure App Service identitások használatával fut.

:::image type="content" alt-text="A tárterület elérésének ábrája." source="./media/scenario-secure-app-access-storage/web-app-access-storage.svg" border="false":::

Hozzá szeretne adni hozzáférést az Azure-adatsíkhoz (Azure Storage, Azure SQL Database, Azure Key Vault vagy más szolgáltatásokhoz) a webalkalmazásból. Használhat megosztott kulcsot is, de utána kell aggódnia annak működési biztonsága miatt, hogy ki hozhatja létre, helyezheti üzembe és kezelheti a titkos kulcsot. Az is lehetséges, hogy a kulcsot beolvassák a GitHubra, ahol a támadók tudják, hogyan keresnek. A webalkalmazás adatokhoz való hozzáférésének biztonságosabb módja a [felügyelt identitások használata.](../active-directory/managed-identities-azure-resources/overview.md)

A felügyelt identitások Azure Active Directory (Azure AD) lehetővé teszik, App Service szerepköralapú hozzáférés-vezérléssel (RBAC) hozzáférjenek az erőforrásokhoz anélkül, hogy alkalmazás-hitelesítő adatokra volna szükség. Miután hozzárendelt egy felügyelt identitást a webalkalmazáshoz, az Azure gondoskodik a tanúsítványok létrehozásáról és terjesztéséről. Az embereknek nem kell aggódniuk a titkos kulcsok vagy az alkalmazás hitelesítő adatainak kezelése miatt.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Rendszer által hozzárendelt felügyelt identitás létrehozása egy webalkalmazásban.
> * Hozzon létre egy tárfiókot és egy Azure Blob Storage tárolót.
> * Tárterület elérése webalkalmazásból felügyelt identitások használatával.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Egy alkalmazáson futó Azure App Service, amely App Service [hitelesítési/engedélyezési modullal rendelkezik.](scenario-secure-app-authentication-app-service.md)

## <a name="enable-managed-identity-on-an-app"></a>Felügyelt identitás engedélyezése egy alkalmazásban

Ha a webalkalmazást a Visual Studio hozza létre és teszi közzé, a felügyelt identitás engedélyezve lett az alkalmazásban. Az App Service-beli bal oldali panelen válassza az **Identitás,** majd a **Rendszer által hozzárendelt lehetőséget.** Ellenőrizze, hogy **az Állapot** be van-e **állítva.** Ha nem, válassza a **Mentés,** majd az **Igen** lehetőséget a rendszer által hozzárendelt felügyelt identitás engedélyezéséhez. Ha a felügyelt identitás engedélyezve van, az állapot **Be** lesz kapcsolva, és az objektumazonosító elérhető.

:::image type="content" alt-text="Képernyőkép a Rendszer által hozzárendelt identitás lehetőségről." source="./media/scenario-secure-app-access-storage/create-system-assigned-identity.png":::

Ez a lépés egy új objektumazonosítót hoz létre, amely eltér a **Hitelesítés/Engedélyezés panelen** létrehozott alkalmazásazonosítótól. Másolja ki a rendszer által hozzárendelt felügyelt identitás objektumazonosítóját. Erre később még szüksége lesz.

## <a name="create-a-storage-account-and-blob-storage-container"></a>Tárfiók és tároló Blob Storage létrehozása

Most már készen áll arra, hogy létrehoz egy tárfiókot, és Blob Storage tárolót.

Minden tárfióknak egy Azure-erőforráscsoporthoz kell tartoznia. Az erőforráscsoport egy logikai tároló az Azure-szolgáltatások csoportosításához. Amikor létrehoz egy tárfiókot, létrehozhat egy új erőforráscsoportot, vagy használhat egy meglévőt. Ez a cikk bemutatja, hogyan hozhat létre új erőforráscsoportot.

Az általános célú v2-tárfiókok az összes Azure Storage-szolgáltatáshoz (blobokhoz, fájlokhoz, üzenetsorokhoz, táblákhoz és lemezekhez) hozzáférést biztosítanak. Az itt ismertetett lépések egy általános célú v2-tárfiókot hoznak létre, de a tárfiókok bármely típusának létrehozási lépései hasonlóak.

Az Azure Storage-beli blobok tárolókba vannak rendezve. Mielőtt az oktatóanyag későbbi lépésében feltölthet egy blobot, létre kell hoznia egy tárolót.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Kövesse az alábbi lépéseket, ha általános célú v2-tárfiókot Azure Portal a tárfiókban.

1. Az Azure Portal menüjében válassza a **Minden szolgáltatás** lehetőséget. Az erőforrások listájában adja meg a **Tárfiókok adhatja meg a következőt:**. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Tárfiókok lehetőséget.**

1. A megjelenő **Tárfiókok** ablakban válassza a Hozzáadás **lehetőséget.**

1. Válassza ki azt az előfizetést, amelyben létre kívánja hozni a tárfiókot.

1. Az **Erőforráscsoport mezőben** válassza ki a webalkalmazást tartalmazó erőforráscsoportot a legördülő menüből.

1. Ezután adja meg a tárfiók nevét. A választott névnek az Azure-on belül egyedinek kell lennie. A névnek 3–24 karakter hosszúságúnak kell lennie, és csak számokat és kisbetűket tartalmazhat.

1. Válassza ki a tárfiókja helyét, vagy használja az alapértelmezett helyet.

1. Ne módosítsa a következő mezők alapértelmezett értékeit:

    |Mező|Érték|
    |--|--|
    |Üzembehelyezési modell|Resource Manager|
    |Teljesítmény|Standard|
    |Fiók altípusa|StorageV2 (általános célú v2)|
    |Replikáció|Írásvédett georedundáns tárolás (RA-GRS)|
    |Hozzáférési szint|Gyakori|

1. A tárfiók beállításainak áttekintéséhez és a fiók létrehozásához válassza a **Felülvizsgálat + létrehozás** elemet.

1. Válassza a **Létrehozás** lehetőséget.

Az azure storage Blob Storage tároló létrehozásához kövesse az alábbi lépéseket.

1. Az új tárfiókot a Azure Portal.

1. A tárfiók bal oldali menüjében görgessen a Blob service **szakaszhoz,** majd válassza a **Tárolók lehetőséget.**

1. Válassza a **+ Tároló** gombot.

1. Adja meg az új tároló nevét. A tároló neve csak kisbetűket tartalmazhat, betűvel vagy számmal kell kezdődnie, és csak betűket, számokat és kötőjelet (-) tartalmazhat.

1. Adja meg a tároló nyilvános hozzáférési szintjét. Az alapértelmezett szint: **Privát (nincs névtelen hozzáférés)**.

1. A tároló létrehozásához válassza az **OK** gombot.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Egy általános célú v2-tárfiók és egy Blob Storage létrehozásához futtassa a következő szkriptet. Adja meg a webalkalmazást tartalmazó erőforráscsoport nevét. Adja meg a tárfiók nevét. A választott névnek az Azure-on belül egyedinek kell lennie. A névnek 3–24 karakter hosszúságúnak kell lennie, és csak számokat és kisbetűket tartalmazhat.

Adja meg a tárfiók helyét. Az előfizetéséhez érvényes helyek listájának a futtatásával ```Get-AzLocation | select Location``` megjelenik. A tároló neve csak kisbetűket tartalmazhat, betűvel vagy számmal kell kezdődnie, és csak betűket, számokat és kötőjelet (-) tartalmazhat.

Ne felejtse el lecserélni a szögletes zárójelben lévő helyőrzőket a saját értékeire.

```powershell
Connect-AzAccount

$resourceGroup = "securewebappresourcegroup"
$location = "<location>"
$storageName="securewebappstorage"
$containerName = "securewebappblobcontainer"

$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2

$ctx = $storageAccount.Context

New-AzStorageContainer -Name $containerName -Context $ctx -Permission blob
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Futtassa a következő szkriptet egy általános célú v2-tárfiók létrehozásához Blob Storage tároló létrehozásához. Adja meg a webalkalmazást tartalmazó erőforráscsoport nevét. Adja meg a tárfiók nevét. A választott névnek az Azure-on belül egyedinek kell lennie. A névnek 3–24 karakter hosszúságúnak kell lennie, és csak számokat és kisbetűket tartalmazhat. 

Adja meg a tárfiók helyét. A tároló neve csak kisbetűket tartalmazhat, betűvel vagy számmal kell kezdődnie, és csak betűket, számokat és kötőjelet (-) tartalmazhat.

Az alábbi példa az Ön Azure AD-fiókjával engedélyezi a tároló létrehozására szolgáló műveletet. A tároló létrehozása előtt rendelje hozzá saját magahoz a Storage-blobadatok közreműködője szerepkört. Még ha Ön is a fiók tulajdonosa, explicit engedélyekre van szüksége ahhoz, hogy adatműveleteket hajtson végre a tárfiókon.

Ne felejtse el lecserélni a szögletes zárójelben lévő helyőrzőket a saját értékeire.

```azurecli-interactive
az login

az storage account create \
    --name securewebappstorage \
    --resource-group securewebappresourcegroup \
    --location <location> \
    --sku Standard_ZRS \
    --encryption-services blob

storageId=$(az storage account show -n securewebappstorage -g securewebappresourcegroup --query id --out tsv)

az ad signed-in-user show --query objectId -o tsv | az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee @- \
    --scope $storageId

az storage container create \
    --account-name securewebappstorage \
    --name securewebappblobcontainer \
    --auth-mode login
```

---

## <a name="grant-access-to-the-storage-account"></a>Hozzáférés megadása a tárfiókhoz

Blobok létrehozása, olvasása vagy törlése előtt hozzáférést kell adnunk a webalkalmazásnak a tárfiókhoz. Az előző lépésben felügyelt identitással konfigurálta a App Service futtató webalkalmazást. Az Azure RBAC használatával hozzáférést adhat a felügyelt identitásnak egy másik erőforráshoz, akárcsak bármely más rendszerbiztonsági tag. A Storage-blobadatok közreműködője szerepkör (amelyet a rendszer által hozzárendelt felügyelt identitás képvisel) olvasási, írási és törlési hozzáférést biztosít a webalkalmazásnak a blobtárolóhoz és az adatokhoz.

# <a name="portal"></a>[Portál](#tab/azure-portal)

A [Azure Portal](https://portal.azure.com)be a tárfiókba, és adjon hozzáférést a webalkalmazásnak. A bal oldali panelen válassza a Hozzáférés-vezérlés **(IAM),** majd a **Szerepkör-hozzárendelések lehetőséget.** Megjelenik a tárfiókhoz hozzáférő felhasználók listája. Most hozzá szeretne adni egy szerepkör-hozzárendelést egy robothoz, az App Service-hez, amely hozzáfér a tárfiókhoz. Válassza **a**  >  **Szerepkör-hozzárendelés hozzáadása lehetőséget.**

A **Szerepkör mezőben** válassza a **Storage-blobadatok** közreműködője lehetőséget, hogy hozzáférést biztosítson a webalkalmazásnak a tárolóblobok olvasására. A **Hozzáférés hozzárendelése hez mezőben** válassza a **App Service** lehetőséget. Az **Előfizetés mezőben** válassza ki az előfizetését. Ezután válassza ki azt az App Service-t, amely számára hozzáférést kíván biztosítani. Kattintson a **Mentés** gombra.

:::image type="content" alt-text="Képernyőkép a Szerepkör-hozzárendelés hozzáadása képernyőről." source="./media/scenario-secure-app-access-storage/add-role-assignment.png":::

A webalkalmazás most már hozzáfér a tárfiókhoz.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az alábbi szkript futtatásával rendelje hozzá a webalkalmazást (amelyet egy rendszer által hozzárendelt felügyelt identitás képvisel) a Storage-blobadatok közreműködője szerepkört a tárfiókon.

```powershell
$resourceGroup = "securewebappresourcegroup"
$webAppName="SecureWebApp20201102125811"
$storageName="securewebappstorage"

$spID = (Get-AzWebApp -ResourceGroupName $resourceGroup -Name $webAppName).identity.principalid
$storageId= (Get-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storageName).Id
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Storage Blob Data Contributor" -Scope $storageId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az alábbi szkript futtatásával rendelje hozzá a webalkalmazást (amelyet egy rendszer által hozzárendelt felügyelt identitás képvisel) a Storage-blobadatok közreműködője szerepkört a tárfiókon.

```azurecli-interactive
spID=$(az resource list -n SecureWebApp20201102125811 --query [*].identity.principalId --out tsv)

storageId=$(az storage account show -n securewebappstorage -g securewebappresourcegroup --query id --out tsv)

az role assignment create --assignee $spID --role 'Storage Blob Data Contributor' --scope $storageId
```

---

## <a name="access-blob-storage-net"></a>Hozzáférési Blob Storage (.NET)

A [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) osztály a kód jogkivonat-hitelesítő adatainak lekérését használja az Azure Storage-ba történő kérések hitelesítése érdekében. Hozza létre a [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) osztály egy példányát, amely a felügyelt identitással lekéri a jogkivonatokat, és csatolja őket a szolgáltatás-ügyfélhez. A következő példakód lehívja a hitelesített jogkivonat hitelesítő adatait, és felhasználja egy szolgáltatás ügyfélobjektumának létrehozására, amely feltölt egy új blobot.

Ha ezt a kódot egy mintaalkalmazás részeként látja, tekintse meg a [githubon](https://github.com/Azure-Samples/ms-identity-easyauth-dotnet-storage-graphapi/tree/main/1-WebApp-storage-managed-identity)található mintát.

### <a name="install-client-library-packages"></a>Ügyféloldali kódtárcsomagok telepítése

Telepítse a [Blob Storage NuGet-csomagot](https://www.nuget.org/packages/Azure.Storage.Blobs/) az Blob Storage és az [Azure Identity .NET NuGet-csomaghoz](https://www.nuget.org/packages/Azure.Identity/) ügyféloldali kódtárával való használathoz az Azure AD hitelesítő adataival való hitelesítéshez. Telepítse az ügyfélkódtárakat a .NET Core parancssori felületével vagy a Csomagkezelő konzollal a Visual Studio.

# <a name="command-line"></a>[Parancssor](#tab/command-line)

Nyisson meg egy parancssort, és váltson arra a könyvtárra, amely a projektfájlt tartalmazza.

Futtassa a telepítési parancsokat.

```dotnetcli
dotnet add package Azure.Storage.Blobs

dotnet add package Azure.Identity
```

# <a name="package-manager"></a>[Csomagkezelő](#tab/package-manager)

Nyissa meg a projektet vagy a megoldást Visual Studio konzolt a **Tools**  >  **NuGet Csomagkezelő** Csomagkezelő Console  >  **paranccsal.**

Futtassa a telepítési parancsokat.
```powershell
Install-Package Azure.Storage.Blobs

Install-Package Azure.Identity
```

---

### <a name="example"></a>Példa

```csharp
using System;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Text;
using System.IO;
using Azure.Identity;

// Some code omitted for brevity.

static public async Task UploadBlob(string accountName, string containerName, string blobName, string blobContents)
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
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            await containerClient.UploadBlobAsync(blobName, stream);
        }
    }
    catch (Exception e)
    {
        throw e;
    }
}
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett ezzel az oktatóanyaggal, és már nincs szüksége a webalkalmazásra vagy a társított erőforrásokra, akkor a létrehozott [erőforrásokat kell megtisztítani.](scenario-secure-app-clean-up-resources.md)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
>
> * Hozzon létre egy rendszer által hozzárendelt felügyelt identitást.
> * Hozzon létre egy tárfiókot és Blob Storage tárolót.
> * Tárterület elérése webalkalmazásból felügyelt identitások használatával.

> [!div class="nextstepaction"]
> [App Service a Microsoft Graph nevében fér hozzá](scenario-secure-app-access-microsoft-graph-as-user.md)

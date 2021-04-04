---
title: Oktatóanyag – a Web App felügyelt identitások használatával fér hozzá a tárolóhoz | Azure
description: Ebből az oktatóanyagból megtudhatja, hogyan érheti el az Azure Storage-t egy alkalmazáshoz a felügyelt identitások használatával.
services: storage, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/30/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: 72b1d4fe864c23c0ac065e47d96ab0c78866defa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96435841"
---
# <a name="tutorial-access-azure-storage-from-a-web-app"></a>Oktatóanyag: az Azure Storage elérése egy webalkalmazásból

Ismerje meg, hogyan érheti el az Azure Storage szolgáltatást a Azure App Serviceon futó webalkalmazásokhoz (nem bejelentkezett felhasználóhoz) felügyelt identitások használatával.

:::image type="content" alt-text="A tároló elérését bemutató diagram." source="./media/scenario-secure-app-access-storage/web-app-access-storage.svg" border="false":::

Hozzáférést szeretne adni az Azure-beli adatsíkon (Azure Storage, Azure SQL Database, Azure Key Vault vagy más szolgáltatásokhoz) a webalkalmazásból. Használhat megosztott kulcsot, de a titkos kulcs létrehozásához, üzembe helyezéséhez és kezeléséhez szükséges működési biztonságot kell aggódnia. Lehetséges, hogy a kulcsot a GitHubon is be lehetett olvasni, így a hackerek tudják, hogyan kereshetnek. A webalkalmazások adathozzáférésének biztonságosabb módja a [felügyelt identitások](../active-directory/managed-identities-azure-resources/overview.md)használata.

Azure Active Directory (Azure AD) felügyelt identitása lehetővé teszi, hogy a App Service szerepköralapú hozzáférés-vezérléssel (RBAC) keresztül hozzáférjenek az erőforrásokhoz az alkalmazás hitelesítő adatainak megadása nélkül. Miután felügyelt identitást rendelt a webalkalmazáshoz, az Azure gondoskodik a tanúsítványok létrehozásáról és elosztásáról. Az embereknek nem kell aggódniuk a titkok vagy az alkalmazások hitelesítő adatainak kezelésével kapcsolatban.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Rendszerhez rendelt felügyelt identitás létrehozása egy webalkalmazásban.
> * Hozzon létre egy Storage-fiókot és egy Azure Blob Storage tárolót.
> * Hozzáférés a tárhelyhez egy webalkalmazásból felügyelt identitások használatával.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Olyan Azure App Service futó webalkalmazás, amelyen engedélyezve van a [app Service hitelesítés/engedélyezési modul](scenario-secure-app-authentication-app-service.md).

## <a name="enable-managed-identity-on-an-app"></a>Felügyelt identitás engedélyezése egy alkalmazáson

Ha a Visual studión keresztül hozza létre és teszi közzé a webalkalmazást, a felügyelt identitás engedélyezve lett az alkalmazásban. Az App Service-ben válassza az **identitás** elemet a bal oldali ablaktáblán, majd válassza a **rendszer hozzárendelve** lehetőséget. Ellenőrizze, hogy az **állapot** be van **-e** állítva. Ha nem, válassza a **Mentés** lehetőséget, majd válassza az **Igen** lehetőséget a rendszer által hozzárendelt felügyelt identitás engedélyezéséhez. Ha a felügyelt identitás engedélyezve van, az állapot be értékre van állítva **, és az** objektumazonosító elérhető.

:::image type="content" alt-text="Képernyőkép, amely a rendszerhez rendelt identitás beállítást jeleníti meg." source="./media/scenario-secure-app-access-storage/create-system-assigned-identity.png":::

Ez a lépés létrehoz egy új objektumazonosítót, amely eltér a **hitelesítés/engedélyezés** ablaktáblán létrehozott alkalmazás-azonosítótól. Másolja a rendszer által hozzárendelt felügyelt identitás objektum-AZONOSÍTÓját. Erre később még szüksége lesz.

## <a name="create-a-storage-account-and-blob-storage-container"></a>Storage-fiók és Blob Storage tároló létrehozása

Most már készen áll a Storage-fiók és a Blob Storage tároló létrehozására.

Minden tárfióknak egy Azure-erőforráscsoporthoz kell tartoznia. Az erőforráscsoport egy logikai tároló az Azure-szolgáltatások csoportosításához. A Storage-fiók létrehozásakor lehetősége van új erőforráscsoport létrehozására vagy egy meglévő erőforráscsoport használatára. Ez a cikk bemutatja, hogyan hozhat létre egy új erőforráscsoportot.

Az általános célú v2-tárfiókok az összes Azure Storage-szolgáltatáshoz (blobokhoz, fájlokhoz, üzenetsorokhoz, táblákhoz és lemezekhez) hozzáférést biztosítanak. Az itt leírt lépések egy általános célú v2-es Storage-fiókot hoznak létre, de a bármilyen típusú Storage-fiók létrehozásának lépései hasonlóak.

Az Azure Storage-beli Blobok tárolóba vannak rendezve. Ahhoz, hogy az oktatóanyag későbbi részében fel lehessen tölteni egy blobot, először létre kell hoznia egy tárolót.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Az alábbi lépéseket követve hozzon létre egy általános célú v2-es Storage-fiókot a Azure Portalban.

1. Az Azure Portal menüjében válassza a **Minden szolgáltatás** lehetőséget. Az erőforrások listájában adja meg a **Storage-fiókokat**. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Storage-fiókok** lehetőséget.

1. A megjelenő **Storage-fiókok** ablakban válassza a **Hozzáadás** lehetőséget.

1. Válassza ki azt az előfizetést, amelyben létre kívánja hozni a tárfiókot.

1. Az **erőforráscsoport** mezőben válassza ki azt az erőforráscsoportot, amely a webalkalmazást tartalmazza a legördülő menüből.

1. Ezután adja meg a tárfiók nevét. A választott névnek az Azure-on belül egyedinek kell lennie. A névnek 3 – 24 karakter hosszúnak kell lennie, és csak számokat és kisbetűket tartalmazhat.

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

Ha Blob Storage tárolót szeretne létrehozni az Azure Storage-ban, kövesse az alábbi lépéseket.

1. Nyissa meg az új Storage-fiókot a Azure Portal.

1. A Storage-fiók bal oldali menüjében görgessen a **blob Service** szakaszra, majd válassza a **tárolók** lehetőséget.

1. Válassza a **+ Tároló** gombot.

1. Adja meg az új tároló nevét. A tároló neve csak kisbetűket tartalmazhat, betűvel vagy számmal kell kezdődnie, és csak betűket, számokat és kötőjelet (-) tartalmazhat.

1. Adja meg a tároló nyilvános hozzáférési szintjét. Az alapértelmezett szint: **Privát (nincs névtelen hozzáférés)**.

1. A tároló létrehozásához válassza az **OK** gombot.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Általános célú v2 Storage-fiók és Blob Storage tároló létrehozásához futtassa az alábbi parancsfájlt. Adja meg a webalkalmazást tartalmazó erőforráscsoport nevét. Adja meg a tárfiók nevét. A választott névnek az Azure-on belül egyedinek kell lennie. A névnek 3 – 24 karakter hosszúnak kell lennie, és csak számokat és kisbetűket tartalmazhat.

Adja meg a Storage-fiók helyét. Az előfizetéséhez érvényes helyszínek listájának megtekintéséhez futtassa a parancsot ```Get-AzLocation | select Location``` . A tároló neve csak kisbetűket tartalmazhat, betűvel vagy számmal kell kezdődnie, és csak betűket, számokat és kötőjelet (-) tartalmazhat.

Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire.

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

Általános célú v2 Storage-fiók és Blob Storage tároló létrehozásához futtassa az alábbi parancsfájlt. Adja meg a webalkalmazást tartalmazó erőforráscsoport nevét. Adja meg a tárfiók nevét. A választott névnek az Azure-on belül egyedinek kell lennie. A névnek 3 – 24 karakter hosszúnak kell lennie, és csak számokat és kisbetűket tartalmazhat. 

Adja meg a Storage-fiók helyét. A tároló neve csak kisbetűket tartalmazhat, betűvel vagy számmal kell kezdődnie, és csak betűket, számokat és kötőjelet (-) tartalmazhat.

A következő példa az Azure AD-fiókját használja, hogy engedélyezze a műveletet a tároló létrehozásához. A tároló létrehozása előtt rendelje hozzá a Storage blob adatközreműködői szerepkört saját magának. Még ha Ön is a fiók tulajdonosa, explicit engedélyekkel kell rendelkeznie a Storage-fiókkal kapcsolatos adatműveletek végrehajtásához.

Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire.

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

## <a name="grant-access-to-the-storage-account"></a>Hozzáférés biztosítása a Storage-fiókhoz

A Blobok létrehozása, olvasása vagy törlése előtt meg kell adnia a webalkalmazáshoz való hozzáférést a Storage-fiókhoz. Az előző lépésben a App Serviceon futó webalkalmazást felügyelt identitással konfigurálta. Az Azure RBAC lehetővé teszi a felügyelt identitás hozzáférését egy másik erőforráshoz, ugyanúgy, mint bármely rendszerbiztonsági tag. A Storage blob adatközreműködői szerepköre a blob-tároló és az adatok olvasási, írási és törlési hozzáférését biztosítja a webalkalmazásnak (amelyet a rendszer által hozzárendelt felügyelt identitás képvisel).

# <a name="portal"></a>[Portál](#tab/azure-portal)

A [Azure Portalban](https://portal.azure.com)nyissa meg a Storage-fiókját a webalkalmazás-hozzáférés biztosításához. A bal oldali ablaktáblán válassza a **hozzáférés-vezérlés (iam)** lehetőséget, majd válassza a **szerepkör-hozzárendelések** lehetőséget. Ekkor megjelenik egy lista, amely hozzáfér a Storage-fiókhoz. Most hozzá kívánja adni a szerepkör-hozzárendelést egy robothoz, az App Service-nek, amelynek hozzá kell férnie a Storage-fiókhoz. Válassza a **Hozzáadás**  >  **szerepkör-hozzárendelés hozzáadása** elemet.

A **szerepkör** területen válassza a **Storage blob-adatközreműködői** lehetőséget, hogy a webalkalmazás hozzáférhessen a Storage-Blobok olvasásához. A **hozzáférés társítása** elemhez válassza a **app Service** lehetőséget. Az **előfizetés** területen válassza ki az előfizetését. Ezután válassza ki azt az App Service-szolgáltatást, amelyhez hozzáférést szeretne biztosítani. Kattintson a **Mentés** gombra.

:::image type="content" alt-text="Képernyőkép, amely a szerepkör-hozzárendelés hozzáadása képernyőt jeleníti meg." source="./media/scenario-secure-app-access-storage/add-role-assignment.png":::

A webalkalmazás mostantól hozzáfér a Storage-fiókhoz.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Futtassa a következő parancsfájlt a webalkalmazás (amelyet egy rendszer által hozzárendelt felügyelt identitás jelöl) a Storage-beli blob adatközreműködői szerepkört a Storage-fiókjában.

```powershell
$resourceGroup = "securewebappresourcegroup"
$webAppName="SecureWebApp20201102125811"
$storageName="securewebappstorage"

$spID = (Get-AzWebApp -ResourceGroupName $resourceGroup -Name $webAppName).identity.principalid
$storageId= (Get-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storageName).Id
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Storage Blob Data Contributor" -Scope $storageId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Futtassa a következő parancsfájlt a webalkalmazás (amelyet egy rendszer által hozzárendelt felügyelt identitás jelöl) a Storage-beli blob adatközreműködői szerepkört a Storage-fiókjában.

```azurecli-interactive
spID=$(az resource list -n SecureWebApp20201102125811 --query [*].identity.principalId --out tsv)

storageId=$(az storage account show -n securewebappstorage -g securewebappresourcegroup --query id --out tsv)

az role assignment create --assignee $spID --role 'Storage Blob Data Contributor' --scope $storageId
```

---

## <a name="access-blob-storage-net"></a>Hozzáférési Blob Storage (.NET)

A [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) osztály a kód jogkivonat-hitelesítő adatainak lekérésére szolgál az Azure Storage-ba irányuló kérelmek engedélyezéséhez. Hozza létre a [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) osztály egy példányát, amely a felügyelt identitás használatával beolvassa a jogkivonatokat, és csatolja őket a szolgáltatás ügyfeléhez. A következő kódrészlet beolvassa a hitelesített jogkivonat hitelesítő adatait, és a használatával létrehoz egy szolgáltatás-ügyfél objektumot, amely feltölt egy új blobot.

Ha ezt a kódot egy minta alkalmazás részeként szeretné látni, tekintse [meg a githubon található mintát](https://github.com/Azure-Samples/ms-identity-easyauth-dotnet-storage-graphapi/tree/main/1-WebApp-storage-managed-identity).

### <a name="install-client-library-packages"></a>Ügyféloldali függvénytár-csomagok telepítése

Telepítse a [blob Storage NuGet csomagot](https://www.nuget.org/packages/Azure.Storage.Blobs/) , hogy működjön együtt a blob Storage és az [Azure Identity ügyféloldali kódtár for .net NuGet csomaggal](https://www.nuget.org/packages/Azure.Identity/) az Azure ad hitelesítő adataival történő hitelesítéshez. Telepítse az ügyféloldali kódtárakat a Visual Studióban a .NET Core parancssori felület vagy a Package Manager konzol használatával.

# <a name="command-line"></a>[Parancssor](#tab/command-line)

Nyisson meg egy parancssort, és váltson arra a könyvtárra, amely tartalmazza a projektfájlt.

Futtassa a telepítési parancsokat.

```dotnetcli
dotnet add package Azure.Storage.Blobs

dotnet add package Azure.Identity
```

# <a name="package-manager"></a>[Csomagkezelő](#tab/package-manager)

Nyissa meg a projektet vagy a megoldást a Visual Studióban, és nyissa meg a **konzolt a**  >  **NuGet Package Manager** csomagkezelő  >  **konzoljának** használatával.

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

Ha elkészült az Oktatóanyaggal, és már nincs szüksége a webalkalmazásra vagy a kapcsolódó erőforrásokra, [törölje a létrehozott erőforrásokat](scenario-secure-app-clean-up-resources.md).

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
>
> * Hozzon létre egy rendszer által hozzárendelt felügyelt identitást.
> * Hozzon létre egy Storage-fiókot és Blob Storage tárolót.
> * Hozzáférés a tárhelyhez egy webalkalmazásból felügyelt identitások használatával.

> [!div class="nextstepaction"]
> [A App Service Microsoft Graph a felhasználó nevében fér hozzá](scenario-secure-app-access-microsoft-graph-as-user.md)
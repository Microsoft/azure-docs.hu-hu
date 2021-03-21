---
title: Rövid útmutató – blob létrehozása az Azure CLI-vel
titleSuffix: Azure Storage
description: Ebből a rövid útmutatóból megtudhatja, hogyan tölthet fel blobokat az Azure Storage-ba az Azure CLI használatával, hogyan töltheti le a blobokat, és hogyan listázhatja a tárolóban lévő blobokat.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 08/17/2020
ms.author: tamram
ms.custom: devx-track-azurecli
ms.openlocfilehash: 04f793f78cef938c31e7a30aad5569a54eb461a1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104613107"
---
# <a name="quickstart-create-download-and-list-blobs-with-azure-cli"></a>Gyors útmutató: Blobok létrehozása, letöltése és listázása az Azure CLI-vel

Az Azure CLI az Azure parancssori felülete, amely Azure-erőforrások kezelésére szolgál. Használhatja a böngészőjében az Azure Cloud Shell-lel. Vagy telepítheti macOS, Linux és Windows rendszeren, és futtathatja a parancssorból. Ebből a rövid útmutatóból megtudhatja, hogyan lehet az Azure CLI használatával adatokat fel- és letölteni az Azure Blob Storage-be.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment-h3.md)]

- Ehhez a cikkhez az Azure CLI 2.0.46 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="authorize-access-to-blob-storage"></a>Hozzáférés engedélyezése a blob Storage-hoz

Engedélyezheti a blob Storage-hoz való hozzáférést az Azure CLI-vel vagy az Azure AD hitelesítő adataival vagy a Storage-fiók elérési kulcsának használatával. Az Azure AD-beli hitelesítő adatok használata ajánlott. Ez a cikk bemutatja, hogyan engedélyezheti a blob Storage-műveleteket az Azure AD-vel.

A blob Storage-hoz kapcsolódó adatműveletekhez használható Azure CLI-parancsok támogatják a `--auth-mode` paramétert, amely lehetővé teszi egy adott művelet engedélyezésének megadását. Állítsa be a `--auth-mode` paramétert az `login` Azure ad hitelesítő adataival való engedélyezéshez. További információ: a [blob-vagy üzenetsor-adatokhoz való hozzáférés engedélyezése az Azure CLI-vel](./authorize-data-operations-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Csak a blob Storage-adatműveletek támogatják a `--auth-mode` paramétert. A kezelési műveletek, például az erőforráscsoport vagy a Storage-fiók létrehozása, automatikusan az Azure AD hitelesítő adatait használják az engedélyezéshez.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy Azure-erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire a szögletes zárójelekben:

```azurecli
az group create \
    --name <resource-group> \
    --location <location>
```

## <a name="create-a-storage-account"></a>Tárfiók létrehozása

Az [az storage account create](/cli/azure/storage/account) paranccsal hozzon létre egy általános célú tárfiókot. Az általános célú tárfiók mind a négy szolgáltatással (blobok, fájlok, táblák és üzenetsorok) használható.

Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire a szögletes zárójelekben:

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_ZRS \
    --encryption-services blob
```

## <a name="create-a-container"></a>Tároló létrehozása

A blobok minden esetben egy tárolóba lesznek feltöltve. A Blobok csoportjait ugyanúgy rendszerezheti a tárolókban, mint ahogyan a fájlokat a számítógépen mappákban rendezi. Hozzon létre blobok tárolására alkalmas tárolót az [az storage container create](/cli/azure/storage/container) parancs segítségével.

A következő példa az Azure AD-fiókját használja, hogy engedélyezze a műveletet a tároló létrehozásához. A tároló létrehozása előtt rendelje hozzá a [Storage blob adatközreműködői](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) szerepkört saját magának. Még ha Ön is a fiók tulajdonosa, explicit engedélyekre van szüksége az adatműveletek a Storage-fiókkal történő végrehajtásához. Az Azure-szerepkörök hozzárendelésével kapcsolatos további információkért lásd: az Azure [CLI használata az Azure-beli szerepkör hozzárendeléséhez a hozzáféréshez](../common/storage-auth-aad-rbac-cli.md?toc=/azure/storage/blobs/toc.json).  

Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire a szögletes zárójelekben:

```azurecli
az ad signed-in-user show --query objectId -o tsv | az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee @- \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"

az storage container create \
    --account-name <storage-account> \
    --name <container> \
    --auth-mode login
```

> [!IMPORTANT]
> Az Azure-beli szerepkör-hozzárendelések eltartása néhány percet is igénybe vehet.

A Storage-fiók kulcsa segítségével engedélyezheti a művelet létrehozását a tároló létrehozásához. Az adatműveletek Azure CLI-vel való engedélyezésével kapcsolatos további információkért lásd: [hozzáférés engedélyezése blob-vagy üzenetsor-adatokhoz az Azure CLI-vel](./authorize-data-operations-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="upload-a-blob"></a>Blob feltöltése

A Blob Storage támogatja a blokkblobokat, a hozzáfűző blobokat és a lapblobokat. Az ebben a rövid útmutatóban szereplő példák azt mutatják be, hogyan használhatók a blokkos Blobok.

Először hozzon létre egy fájlt, amelyet egy blokk blobba kíván feltölteni. Ha Azure Cloud Shell használ, a következő paranccsal hozhat létre egy fájlt:

```bash
vi helloworld
```

A fájl megnyitásakor nyomja le a **Beszúrás** gombot. Írja be a *Hello World* értéket, majd nyomja le az **ESC** billentyűt. Ezután írja be a következőt *: x*, majd nyomja le az **ENTER** billentyűt.

Ebben a példában egy blobot töltünk fel a legutóbbi lépésben, az [az storage blob upload](/cli/azure/storage/blob) paranccsal létrehozott tárolóba. A fájl elérési útját nem szükséges megadnia, mert a fájl a gyökérkönyvtárban lett létrehozva. Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire a szögletes zárójelekben:

```azurecli
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --name helloworld \
    --file helloworld \
    --auth-mode login
```

Ez az eljárás létrehozza a blobot, ha az még nem létezett, és felülírja, ha már igen. Mielőtt továbblépne, töltsön fel annyi fájlt, amennyit csak szeretne.

Ha egyszerre több fájlt szeretne feltölteni, használhatja az [az storage blob upload-batch](/cli/azure/storage/blob) parancsot.

## <a name="list-the-blobs-in-a-container"></a>Tárolóban lévő blobok kilistázása

Listázza ki a tárolóban található blobokat az [az storage blob list](/cli/azure/storage/blob) paranccsal. Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire a szögletes zárójelekben:

```azurecli
az storage blob list \
    --account-name <storage-account> \
    --container-name <container> \
    --output table \
    --auth-mode login
```

## <a name="download-a-blob"></a>Blob letöltése

Az [az storage blob download](/cli/azure/storage/blob) paranccsal letöltheti a korábban feltöltött blobot. Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire a szögletes zárójelekben:

```azurecli
az storage blob download \
    --account-name <storage-account> \
    --container-name <container> \
    --name helloworld \
    --file ~/destination/path/for/file \
    --auth-mode login
```

## <a name="data-transfer-with-azcopy"></a>Adatátvitel az AzCopy használatával

A AzCopy parancssori segédprogram nagy teljesítményű, parancsfájl-továbbítást biztosít az Azure Storage-hoz. A AzCopy használatával adatok vihetők át a blob Storage-ba és a Azure Filesba. A AzCopy legújabb verziójával kapcsolatos további információkért lásd: AzCopy v10, a [AzCopy első lépései](../common/storage-use-azcopy-v10.md). Az AzCopy v10 blob Storage-mel történő használatáról további információt az [adatok átvitele a AzCopy és a blob Storage](../common/storage-use-azcopy-v10.md#transfer-data)szolgáltatással című témakörben talál.

A következő példa a AzCopy-t használja egy helyi fájl blobba való feltöltéséhez. Ne felejtse el lecserélni a minták értékeit a saját értékeire:

```bash
azcopy login
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni szeretné a rövid útmutató részeként létrehozott erőforrásokat, beleértve a Storage-fiókot, törölje az erőforráscsoportot az az [Group delete](/cli/azure/group) paranccsal. Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire a szögletes zárójelekben:

```azurecli
az group delete \
    --name <resource-group> \
    --no-wait
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan vihetők át fájlok egy helyi fájlrendszer és egy tároló az Azure Blob Storage szolgáltatásban. Ha többet szeretne megtudni a blob Storage Azure CLI használatával történő használatáról, tekintse meg az Azure CLI-mintákat a blob Storage-hoz.

> [!div class="nextstepaction"]
> [Azure CLI-minták a blob Storage-hoz](./storage-samples-blobs-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

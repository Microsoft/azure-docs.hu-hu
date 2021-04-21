---
title: Válassza ki, hogyan engedélyezze a blobadatokhoz való hozzáférést az Azure CLI-val
titleSuffix: Azure Storage
description: Adja meg, hogyan engedélyezze az adatműveleteket a blobadatokon az Azure CLI használatával. Az adatműveleteket Azure AD-beli hitelesítő adatokkal, a fiók hozzáférési kulcsával vagy közös hozzáférésű jogosultság jogosultsági (SAS) jogkivonattal engedélyezheti.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 53ad6dd922c1ccebd79aebcd2966b23b38de00e7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776896"
---
# <a name="choose-how-to-authorize-access-to-blob-data-with-azure-cli"></a>Válassza ki, hogyan engedélyezze a blobadatokhoz való hozzáférést az Azure CLI-val

Az Azure Storage olyan bővítményeket biztosít az Azure CLI-hez, amelyek lehetővé teszik annak megadását, hogyan szeretné engedélyezni a műveleteket a blobadatokon. Az adatműveleteket a következő módokon engedélyezheti:

- Egy Azure Active Directory (Azure AD) rendszerbiztonsági taggal. A Microsoft az Azure AD hitelesítő adatainak használatát javasolja a kiváló biztonság és könnyű használat érdekében.
- A fiók hozzáférési kulcsával vagy egy közös hozzáférésű jogosultság jogosultsági (SAS) jogkivonattal.

## <a name="specify-how-data-operations-are-authorized"></a>Az adatműveletek engedélyének megadása

A blobadatok olvasásakor és írásában használható Azure CLI-parancsok tartalmazzák a választható `--auth-mode` paramétert. Adja meg ezt a paramétert az adatműveletek engedélyének megadásához:

- Állítsa a `--auth-mode` paramétert a következőre: Azure AD-beli rendszerbiztonsági `login` tag használatával való bejelentkezéshez (ajánlott).
- Állítsa a paramétert az örökölt értékre, hogy megpróbálja lekérni a fiók hozzáférési kulcsát `--auth-mode` `key` az engedélyezéshez. Ha kihagyja a paramétert, az Azure CLI megpróbálja lekérni `--auth-mode` a hozzáférési kulcsot is.

A paraméter használata érdekében győződjön meg arról, hogy az `--auth-mode` Azure CLI 2.0.46-os vagy újabb verziója van telepítve. A `az --version` telepített verzió ellenőrzéshez futtassa a (futtatás) futtatását.

> [!NOTE]
> Ha egy tárfiók zárolva van egy **readOnly** Azure Resource Manager zárolással, a tárfiók nem engedélyezi a Kulcslista műveletet. [](/rest/api/storagerp/storageaccounts/listkeys) **A kulcslista** egy POST művelet, és minden POST-művelet le van akadályozva, ha **readOnly** zárolás van konfigurálva a fiókhoz. Emiatt, ha a fiók **ReadOnly** zárolással van zárolva, a fiókkulcsokkal még nem rendelkező felhasználóknak Azure AD hitelesítő adatokat kell használniuk a blobadatok eléréséhez.

> [!IMPORTANT]
> Ha kihagyja a paramétert, vagy a értékét a következőre adja meg: , akkor az Azure CLI megkísérli a fiók hozzáférési kulcsát `--auth-mode` `key` használni az engedélyezéshez. Ebben az esetben a Microsoft azt javasolja, hogy adja meg a hozzáférési kulcsot a parancsban vagy a AZURE_STORAGE_KEY **környezeti** változóban. További információ a környezeti változókról: Környezeti változók [beállítása engedélyezési paraméterekhez.](#set-environment-variables-for-authorization-parameters)
>
> Ha nem adja meg a hozzáférési kulcsot, az Azure CLI megkísérli az Azure Storage erőforrás-szolgáltatójának hívását, hogy lekéri azt minden művelethez. Számos olyan adatművelet végrehajtása, amely az erőforrás-szolgáltató hívását igényli, szabályozást eredményezhet. További információ az erőforrás-szolgáltató korlátairól: Skálázhatósági és [teljesítménycélok az Azure Storage erőforrás-szolgáltató számára.](../common/scalability-targets-resource-provider.md)

## <a name="authorize-with-azure-ad-credentials"></a>Hitelesítés Azure AD hitelesítő adatokkal

Amikor Azure AD hitelesítő adatokkal jelentkezik be az Azure CLI-be, a rendszer OAuth 2.0 hozzáférési jogkivonatot ad vissza. Ezt a jogkivonatot az Azure CLI automatikusan használja a Blob- vagy Queue Storage-beli későbbi adatműveletek végrehajtásához. A támogatott műveletekhez már nem kell fiókkulcsot vagy SAS-jogkivonatot átadnia az paranccsal.

A blobadatokhoz az Azure szerepköralapú hozzáférés-vezérlésével (Azure RBAC) rendelhet engedélyeket egy Azure AD-rendszerbiztonsági taghoz. További információ az Azure Storage Azure-szerepköreiről: Az Azure Storage-adatok hozzáférési jogosultságának kezelése [az Azure RBAC használatával.](../common/storage-auth-aad-rbac-portal.md)

### <a name="permissions-for-calling-data-operations"></a>Adatműveletek hívására vonatkozó engedélyek

Az Azure Storage-bővítmények a blobadatokon való műveletekhez támogatottak. A hívható műveletek az Azure AD rendszerbiztonsági tagnak megadott engedélyektől függnek, amellyel bejelentkezik az Azure CLI-be. Az Azure Storage-tárolókra vonatkozó engedélyeket az Azure RBAC-n keresztül lehet hozzárendelni. Ha például a Storage **Blob** adatolvasó szerepköre van hozzárendelve, akkor olyan parancsokat futtathat, amelyek egy tárolóból olvasnak adatokat. Ha a **Storage-blobadatok** közreműködője szerepkört kap, akkor futtathat olyan parancsprogram-parancsokat, amelyek egy tárolót vagy az abban lévő adatokat olvasnak, írnak vagy törölnek.

A tárolók egyes Azure Storage-műveleteihez szükséges engedélyekkel kapcsolatos részletekért lásd: Call storage operations with OAuth tokens (Tárolási műveletek hívása [OAuth-jogkivonatokkal).](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens)  

### <a name="example-authorize-an-operation-to-create-a-container-with-azure-ad-credentials"></a>Példa: Azure AD hitelesítő adatokkal rendelkező tároló létrehozására szolgáló művelet hitelesítése

Az alábbi példa bemutatja, hogyan hozhat létre tárolót az Azure CLI-ről az Azure AD hitelesítő adataival. A tároló létrehozásához be kell jelentkeznie az Azure CLI-be, és szüksége lesz egy erőforráscsoportra és egy tárfiókra. Az erőforrások létrehozásáról lásd: Rövid [útmutató: Blobok létrehozása,](../blobs/storage-quickstart-blobs-cli.md)letöltése és listása az Azure CLI használatával.

1. A tároló létrehozása előtt rendelje hozzá saját magahoz a [Storage-blobadatok közreműködője](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) szerepkört. Annak ellenére, hogy Ön a fiók tulajdonosa, explicit engedélyekre van szüksége ahhoz, hogy adatműveleteket hajtson végre a tárfiókon. Az Azure-szerepkörök hozzárendelésével kapcsolatos további információkért lásd: Azure Portal azure-beli szerepkör hozzárendelése a blob- és üzenetsoradatokhoz [való hozzáféréshez.](../common/storage-auth-aad-rbac-portal.md)

    > [!IMPORTANT]
    > Az Azure-beli szerepkör-hozzárendelések propagálása eltarthat néhány percig.

1. Hívja meg [az az storage container create](/cli/azure/storage/container#az_storage_container_create) parancsot a(z) paraméterrel, hogy létrehozza a `--auth-mode` tárolót az Azure AD hitelesítő `login` adataival. Ne felejtse el lecserélni a szögletes zárójelben lévő helyőrzőértékeket a saját értékeire:

    ```azurecli
    az storage container create \
        --account-name <storage-account> \
        --name sample-container \
        --auth-mode login
    ```

## <a name="authorize-with-the-account-access-key"></a>Jogosultság a fiók hozzáférési kulcsával

Ha rendelkezik a fiókk kulccsal, bármilyen Azure Storage-adatműveletet hívhat. A fiókkulcs használata általában kevésbé biztonságos. Ha a fiókkulcs biztonsága sérül, a fiókban minden adat biztonsága sérülhet.

Az alábbi példa bemutatja, hogyan hozhat létre tárolót a fiók hozzáférési kulcsával. Adja meg a fiókkulcsot, és adja meg `--auth-mode` a paraméter `key` értékét:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --account-key <key>
    --auth-mode key
```

> [!IMPORTANT]
> Ha egy tárfiók zárolva van egy **readOnly** Azure Resource Manager zárolással, a tárfiók nem engedélyezi a Kulcslista műveletet. [](/rest/api/storagerp/storageaccounts/listkeys) **A kulcslista** egy POST művelet, és minden POST-művelet le van akadályozva, ha **readOnly** zárolás van konfigurálva a fiókhoz. Ezért ha a fiók **ReadOnly** zárolással van zárolva, a felhasználóknak Azure AD hitelesítő adatokkal kell hozzáférniük az adatokhoz.

## <a name="authorize-with-a-sas-token"></a>Jogosultság SAS-jogkivonattal

Ha rendelkezik SAS-jogkivonattal, az SAS által engedélyezett adatműveleteket hívhat meg. Az alábbi példa bemutatja, hogyan hozhat létre tárolót SAS-jogkivonat használatával:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --sas-token <token>
```

## <a name="set-environment-variables-for-authorization-parameters"></a>Környezeti változók beállítása engedélyezési paraméterekhez

A környezeti változókban engedélyezési paramétereket is megadhat, hogy ne kelljen őket az Azure Storage-adatműveletek minden hívásában megadni. Az alábbi táblázat az elérhető környezeti változókat ismerteti.

| Környezeti változó                  | Description                                                                                                                                                                                                                                                                                                                                                                     |
|---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    AZURE_STORAGE_ACCOUNT              |    A tárfiók neve. Ezt a változót a tárfiók kulcsával vagy egy SAS-jogkivonattal együtt kell használni. Ha egyik sem, az Azure CLI megkísérli lekérni a tárfiók hozzáférési kulcsát a hitelesített Azure AD-fiók használatával. Ha egyszerre nagy számú parancsot hajt végre, az Azure Storage erőforrás-szolgáltató szabályozási korlátja el lehet érve. További információ az erőforrás-szolgáltató korlátairól: Az Azure Storage erőforrás-szolgáltató skálázhatósági és [teljesítménycéljai.](../common/scalability-targets-resource-provider.md)             |
|    AZURE_STORAGE_KEY                  |    A tárfiókkulcs. Ezt a változót a tárfiók nevével együtt kell használni.                                                                                                                                                                                                                                                                          |
|    AZURE_STORAGE_CONNECTION_STRING    |    Egy kapcsolati sztring, amely tartalmazza a tárfiók kulcsát vagy egy SAS-jogkivonatot. Ezt a változót a tárfiók nevével együtt kell használni.                                                                                                                                                                                                                       |
|    AZURE_STORAGE_SAS_TOKEN            |    Közös hozzáférésű jogosultság jogosultsága (SAS) jogkivonata. Ezt a változót a tárfiók nevével együtt kell használni.                                                                                                                                                                                                                                                            |
|    AZURE_STORAGE_AUTH_MODE            |    Az engedélyezési mód, amellyel a parancsot futtatni kell. Az engedélyezett értékek `login` a (ajánlott) vagy `key` a . Ha a értéket adja meg, az Azure CLI az Azure AD hitelesítő adatait használja `login` az adatművelet hitelesítéséhez. Ha az örökölt módot adja meg, az Azure CLI megkísérli lekérdezni a fiók hozzáférési kulcsát, és engedélyezni a `key` parancsot a kulccsal.    |

## <a name="next-steps"></a>Következő lépések

- [Azure-szerepkör hozzárendelése blob- és üzenetsoradatokhoz az Azure CLI használatával](../common/storage-auth-aad-rbac-cli.md)
- [Blob- és üzenetsoradatok hozzáférésének jogosultsága az Azure-erőforrások felügyelt identitásával](../common/storage-auth-aad-msi.md)
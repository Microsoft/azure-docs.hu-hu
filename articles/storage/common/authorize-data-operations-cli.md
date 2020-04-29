---
title: Hozzáférés engedélyezése a blobhoz vagy az üzenetsor-kezeléshez az Azure CLI-vel
titleSuffix: Azure Storage
description: Itt adhatja meg, hogy az Azure CLI-vel hogyan lehet engedélyezni az adatműveleteket a blob-vagy üzenetsor-adatokon. Az adatműveletek az Azure AD hitelesítő adataival, a fiók hozzáférési kulcsával vagy egy közös hozzáférés-aláírási (SAS-) jogkivonattal is engedélyezhető.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/26/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: c7091592f8806b6f6655315ae1faace286c2c1f5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78207692"
---
# <a name="authorize-access-to-blob-or-queue-data-with-azure-cli"></a>Hozzáférés engedélyezése a blobhoz vagy az üzenetsor-kezeléshez az Azure CLI-vel

Az Azure Storage olyan bővítményeket biztosít az Azure CLI-hez, amelyek segítségével megadhatja, hogyan kívánja engedélyezni a műveleteket a blob-vagy üzenetsor-adatokon. A következő módokon engedélyezheti az adatműveleteket:

- Egy Azure Active Directory (Azure AD) rendszerbiztonsági tag. A Microsoft az Azure AD-beli hitelesítő adatok használatát javasolja a kiváló biztonság és a könnyű használat érdekében.
- A fiók-hozzáférési kulccsal vagy egy közös hozzáférésű aláírási (SAS-) jogkivonattal.

## <a name="specify-how-data-operations-are-authorized"></a>Az adatműveletek engedélyezésének meghatározása

A blob-és üzenetsor-információk olvasására és írására szolgáló Azure CLI `--auth-mode` -parancsok tartalmazzák a választható paramétert. Adja meg ezt a paramétert, hogy jelezze, hogyan legyen engedélyezve az adatművelet:

- Állítsa be `--auth-mode` a paramétert `login` úgy, hogy bejelentkezzen egy Azure ad rendszerbiztonsági tag használatával (ajánlott).
- Állítsa a `--auth-mode` paramétert az örökölt `key` értékre, hogy megpróbálja lekérni az engedélyezéshez használni kívánt fiók-hozzáférési kulcsot. Ha kihagyja a `--auth-mode` paramétert, akkor az Azure CLI a hozzáférési kulcs lekérését is megkísérli.

A paraméter használatához `--auth-mode` győződjön meg arról, hogy telepítette az Azure CLI 2.0.46 vagy újabb verzióját. Futtassa `az --version` a parancsot a telepített verziójának vizsgálatához.

> [!IMPORTANT]
> Ha kihagyja a `--auth-mode` paramétert, vagy beállítja `key`a értékre, az Azure CLI a fiók hozzáférési kulcsát próbálja használni az engedélyezéshez. Ebben az esetben a Microsoft azt javasolja, hogy a hozzáférési kulcsot a parancson vagy a **AZURE_STORAGE_KEY** környezeti változóban adja meg. A környezeti változókról további információt a [környezeti változók beállítása az engedélyezési paraméterekhez](#set-environment-variables-for-authorization-parameters)című szakaszban talál.
>
> Ha nem adja meg a hozzáférési kulcsot, az Azure CLI megkísérli meghívni az Azure Storage erőforrás-szolgáltatót az egyes műveletek lekéréséhez. Az erőforrás-szolgáltató hívását igénylő számos adatművelet végrehajtása szabályozást eredményezhet. Az erőforrás-szolgáltatói korlátokkal kapcsolatos további információkért tekintse [meg az Azure Storage erőforrás-szolgáltató skálázhatósági és teljesítménybeli céljait](scalability-targets-resource-provider.md)ismertető témakört.

## <a name="authorize-with-azure-ad-credentials"></a>Engedélyezés Azure AD-beli hitelesítő adatokkal

Ha Azure AD-beli hitelesítő adatokkal jelentkezik be az Azure CLI-be, az OAuth 2,0 hozzáférési tokent ad vissza. Ezt a tokent a rendszer automatikusan használja az Azure CLI-vel, hogy engedélyezze a további adatműveleteket a blob vagy a várólista-tárolón. A támogatott műveletek esetében már nem kell átadnia egy fiók kulcsát vagy SAS-jogkivonatát a paranccsal.

Az Azure AD rendszerbiztonsági tag számára a szerepköralapú hozzáférés-vezérlés (RBAC) segítségével engedélyeket rendelhet a blob-és üzenetsor-szolgáltatásokhoz. Az Azure Storage RBAC szerepköreivel kapcsolatos további információkért lásd: [hozzáférési jogosultságok kezelése az Azure Storage-adatokhoz a RBAC használatával](storage-auth-aad-rbac.md).

### <a name="permissions-for-calling-data-operations"></a>Az adatműveletek meghívására vonatkozó engedélyek

Az Azure Storage-bővítmények támogatottak a blob-és üzenetsor-adatokon végzett műveletekhez. A hívható műveletek attól függnek, hogy az Azure AD rendszerbiztonsági tag milyen engedélyekkel rendelkezik, amelyekkel bejelentkezhet az Azure CLI-be. Az Azure Storage-tárolók vagy-várólisták engedélyei a RBAC-on keresztül vannak hozzárendelve. Ha például a **blob-Adatolvasó** szerepkört rendeli hozzá, akkor futtathat parancsfájl-parancsokat, amelyek egy tárolóból vagy várólistából olvasnak be egy adatforrást. Ha a **blob adatközreműködői** szerepkört rendelte hozzá, akkor futtathat olyan parancsfájl-parancsokat, amelyek egy tárolót vagy várólistát, illetve a bennük található adat olvasását, írását vagy törlését írják le.

További információ az egyes Azure Storage-műveletekhez szükséges engedélyekről a tárolón vagy a várólistán: [tárolási műveletek hívása OAuth-jogkivonatokkal](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

### <a name="example-authorize-an-operation-to-create-a-container-with-azure-ad-credentials"></a>Példa: művelet engedélyezése tároló létrehozásához Azure AD-beli hitelesítő adatokkal

Az alábbi példa bemutatja, hogyan hozhat létre tárolót az Azure CLI-vel az Azure AD-beli hitelesítő adataival. A tároló létrehozásához be kell jelentkeznie az Azure CLI-be, és szüksége lesz egy erőforráscsoport és egy Storage-fiókra. Az erőforrások létrehozásával kapcsolatos további információkért lásd: rövid útmutató [: Blobok létrehozása, letöltése és listázása az Azure CLI-vel](../blobs/storage-quickstart-blobs-cli.md).

1. A tároló létrehozása előtt rendelje hozzá a [Storage blob adatközreműködői](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) szerepkört saját magának. Annak ellenére, hogy Ön a fiók tulajdonosa, explicit engedélyekkel kell rendelkeznie az adatműveletek elvégzéséhez a Storage-fiókon. A RBAC-szerepkörök hozzárendelésével kapcsolatos további információkért lásd: [hozzáférés biztosítása az Azure blobhoz és üzenetsor-adatokhoz a Azure Portal RBAC](storage-auth-aad-rbac.md)használatával.

    > [!IMPORTANT]
    > A RBAC szerepkör-hozzárendelések eltartása néhány percet is igénybe vehet.

1. Az Azure AD-beli hitelesítő adataival `--auth-mode` hozza létre a `login` tárolót az az [Storage Container Create](/cli/azure/storage/container#az-storage-container-create) paranccsal, és állítsa a paramétert az értékre. Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire a szögletes zárójelekben:

    ```azurecli
    az storage container create \
        --account-name <storage-account> \
        --name sample-container \
        --auth-mode login
    ```

## <a name="authorize-with-the-account-access-key"></a>Engedélyezés a fiók hozzáférési kulcsával

Ha rendelkezik a fiók kulcsával, meghívhat bármilyen Azure Storage-adatműveletet. Általánosságban elmondható, hogy a fiók kulcsának használata kevésbé biztonságos. Ha a fiók kulcsa biztonságban van, a fiókban lévő összes adattal kapcsolatban lehet sérült.

Az alábbi példa bemutatja, hogyan hozhat létre egy tárolót a fiók hozzáférési kulcsa alapján. Adja meg a fiók kulcsát, és `--auth-mode` adja meg a `key` paramétert az értékkel:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --account-key <key>
    --auth-mode key
```

## <a name="authorize-with-a-sas-token"></a>Engedélyezés SAS-jogkivonattal

Ha SAS-jogkivonattal rendelkezik, meghívhatja az SAS által engedélyezett adatműveleteket. Az alábbi példa bemutatja, hogyan hozhat létre egy tárolót SAS-token használatával:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --sas-token <token>
```

## <a name="set-environment-variables-for-authorization-parameters"></a>Környezeti változók beállítása az engedélyezési paraméterekhez

A környezeti változókban megadhatja az engedélyezési paramétereket, így elkerülhető, hogy azok az Azure Storage-adatműveletek minden hívásán bekerüljenek. Az alábbi táblázat az elérhető környezeti változókat ismerteti.

| Környezeti változó                  | Leírás                                                                                                                                                                                                                                                                                                                                                                     |
|---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    AZURE_STORAGE_ACCOUNT              |    A tárfiók neve. Ezt a változót a Storage-fiók kulcsával vagy egy SAS-tokenrel együtt kell használni. Ha egyik sincs jelen, az Azure CLI a hitelesített Azure AD-fiók használatával megkísérli lekérni a Storage-fiók hozzáférési kulcsát. Ha egyszerre sok parancsot hajt végre, az Azure Storage erőforrás-szolgáltató szabályozási korlátja is elérhető. Az erőforrás-szolgáltatói korlátokkal kapcsolatos további információkért tekintse [meg az Azure Storage erőforrás-szolgáltató skálázhatósági és teljesítménybeli céljait](scalability-targets-resource-provider.md)ismertető témakört.             |
|    AZURE_STORAGE_KEY                  |    A tárfiókkulcs. Ezt a változót a Storage-fiók nevével együtt kell használni.                                                                                                                                                                                                                                                                          |
|    AZURE_STORAGE_CONNECTION_STRING    |    Egy kapcsolati sztring, amely tartalmazza a Storage-fiók kulcsát vagy egy SAS-tokent. Ezt a változót a Storage-fiók nevével együtt kell használni.                                                                                                                                                                                                                       |
|    AZURE_STORAGE_SAS_TOKEN            |    Közös hozzáférésű aláírási (SAS) jogkivonat. Ezt a változót a Storage-fiók nevével együtt kell használni.                                                                                                                                                                                                                                                            |
|    AZURE_STORAGE_AUTH_MODE            |    Az engedélyezési mód, amellyel a parancsot futtatni kívánja. A megengedett értékek `login` a következők: ( `key`ajánlott) vagy. Ha megadja `login`, az Azure CLI az Azure ad hitelesítő adatait használja az adatok műveletének engedélyezéséhez. Ha az örökölt `key` módot adta meg, az Azure CLI megkísérli lekérdezni a fiók hozzáférési kulcsát, és engedélyezi a parancsot a kulccsal.    |

## <a name="next-steps"></a>További lépések

- [Az Azure CLI használata RBAC-szerepkör hozzárendeléséhez a blob-és üzenetsor-adathoz való hozzáféréshez](storage-auth-aad-rbac-cli.md)
- [Hozzáférés engedélyezése a blob-és üzenetsor-szolgáltatásokhoz az Azure-erőforrások felügyelt identitásával](storage-auth-aad-msi.md)
